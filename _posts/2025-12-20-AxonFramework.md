---
layout: post
title: Axon Framework
category: 计算机
---



# Axon

官方：https://docs.axoniq.io/axon-framework-reference/4.12/

对Axon框架的一个简单理解就是：

凭着直觉要完全的融汇贯通AXON的设计哲学，可能需要更深入的理解DDD。而领域驱动设计是需要大量实践的。所以此处先以一种不太专业的形式来介绍AXON。

计算机的基本抽象是：输入-处理-输出。稍微细化一点到编程语言层面则是：入参-函数调用-结果返回。通常我们是将函数调用直接编码到程序中的。AXON可以简化理解为消息中间件。发消息-axon服务-消费消息。将函数调用的过程给解耦了。一般的消息中间件往往涉及单独的服务，而AXON则既可以运行在同一个JVM实例中，也可以运行在不同的JVM实例。

1.同一JVM实例：

A处发送消息到AXON BUS，AXON BUS 再通过匹配规则调用B处代码处理消息。

2.不同JVM实例：

A处发消息到本地的 BUS，BUS将消息通过MQ传输到B处BUS，B处BUS反序列化消息后，交由对应handler处理。



基于对当前系统的认知：AXON因为可以不用跨JVM所以，吞吐量惊人。同时因为同一的规范，提供了极强的统一处理。可以简化理解为是一套自成系统的切面。



在官方文档中，无论是DDD还是AXON，都绕不开Aggregate，但鉴于当前系统中并没有完全的实践Aggregate，故整篇文章都不会过多解释Aggregate。



# 架构全景与运行时核心

![AXON简介251109](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/AXON%E7%AE%80%E4%BB%8B251109.jpg)







![AXON总结](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/AXON%E6%80%BB%E7%BB%93.jpg)







![image-20251109161759984](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251109161759984.png)



## CQRS/ES模式在Axon中的具象化



CQRS（Command Query Responsibility Segregation）：命令查询职责分离

ES（Event Sourcing）：事件溯源



Query：

发送查询消息

```java
		@GetMapping("/foodCart/{foodCartId}")
    public CompletableFuture<FoodCartView> handle(@PathVariable("foodCartId")String foodCartId) {
        return queryGateway.query(new FindFoodCartQuery(
                UUID.fromString(foodCartId)), ResponseTypes.instanceOf(FoodCartView.class));
    }
```

处理查询消息

```java
		@QueryHandler
    public FoodCartView handle(FindFoodCartQuery query) {
        return repository.findById(query.getFoodCartId()).orElse(null);
    }

```



Command:

发送命令消息

```java
		@PostMapping("/create")
    public void handle() {
        commandGateway.send(new CreateFoodCartCommand());
    }
```

处理命令消息

```java
		@CommandHandler
    public FoodCart(CreateFoodCartCommand cmd) {
        UUID aggregateId = UUID.randomUUID();
        AggregateLifecycle.apply(new FoodCartCreatedEvent(aggregateId));
    }
```





Event:

发送事件消息

```java
AggregateLifecycle.apply(new FoodCartCreatedEvent(aggregateId));
```

处理事件消息

```java
		@EventHandler
    public void on(FoodCartCreatedEvent event) {
        FoodCartView foodCartView = new FoodCartView(event.getFoodCartId(), Collections.EMPTY_MAP);
        repository.save(foodCartView);
    }
```

事件溯源

```java
		@EventSourcingHandler
    public void on(FoodCartCreatedEvent event) {
        this.foodCartId = event.getFoodCartId();
        selectedProducts = new HashMap<>();
    }
```



比较标准的CQRS/ES实践应该是：

1.Aggregate作为ES的核心载体，作为推动一切的状态机，在聚合对象中处理处理命令，发布事件，处理事件，及事件溯源。

2.事件存储是溯源的基础。完整的重建流程应该是：从事件持久化仓库中获取所有事件，进行事件重放（EventSourcingHandler）还原为对应的状态，再然后进行对应的操作。从而实现状态机的全流程追踪。

```java
package wiki.liaozk.command;

import org.axonframework.commandhandling.CommandHandler;
import org.axonframework.eventsourcing.EventSourcingHandler;
import org.axonframework.modelling.command.AggregateIdentifier;
import org.axonframework.modelling.command.AggregateLifecycle;
import org.axonframework.spring.stereotype.Aggregate;
import wiki.liaozk.coreapi.*;

import java.util.HashMap;
import java.util.Map;
import java.util.UUID;

@Aggregate
public class FoodCart {

    @AggregateIdentifier
    private UUID foodCartId;
    private Map<UUID, Integer> selectedProducts;

    public FoodCart() {
        //Req By axon
    }

    @CommandHandler
    public FoodCart(CreateFoodCartCommand cmd) {
        UUID aggregateId = UUID.randomUUID();
        AggregateLifecycle.apply(new FoodCartCreatedEvent(aggregateId));
    }

    @CommandHandler
    public void handle(SelectProductCommand cmd) {
        AggregateLifecycle.apply(new ProductSelectedEvent(this.foodCartId, cmd.getProductId(), cmd.getQuantity()));
    }

    @CommandHandler
    public void handle(DeselectProductCommand cmd) throws ProductDeselectionException {
        UUID productId = cmd.getProductId();
        int quantity = cmd.getQuantity();

        if (!selectedProducts.containsKey(productId)) {
            throw new ProductDeselectionException(
                    "当前产品未被选中，无法取消"
            );
        }
        if (selectedProducts.get(productId) - quantity < 0) {
            throw new ProductDeselectionException(
                    "产品"+ productId +"数量不足，无法取消"
            );
        }

        AggregateLifecycle.apply(new ProductDeselectedEvent(foodCartId, productId, quantity));

    }

    @EventSourcingHandler
    public void on(FoodCartCreatedEvent event) {
        this.foodCartId = event.getFoodCartId();
        selectedProducts = new HashMap<>();
    }

    @EventSourcingHandler
    public void on(ProductSelectedEvent event) {
        selectedProducts.merge(event.getProductId(), event.getQuantity(), Integer::sum);
    }
}
```





## **核心组件**

### 总线的通信模型

command：默认是同步的，语义决定。默认commandBus就是同步通信的，即使返回的是future也是使用send线程去调用handler的。也就是这里是伪异步的。

```java
public <R> CompletableFuture<R> send(CommandMessage<?> command) {
    // 1. validate, intercept, resolve handler...
    MessageHandler<? super CommandMessage<?>> handler = resolveHandler(command);

    // 2. 直接 invoke handler in current thread!
    try {
        R result = (R) handler.handle(command);  // ← 同步调用！阻塞当前线程！
        return CompletableFuture.completedFuture(result);  // ← 已完成的 future！
    } catch (Exception e) {
        CompletableFuture<R> future = new CompletableFuture<>();
        future.completeExceptionally(e);
        return future;
    }
}
```



Event：天然异步。对于系统，使用的默认eventbus，同时是TrackingEventProcessor消费事件（SubscribingEventProcessor是同步的，但一般不使用！）。对于track方式消费事件：
1.有独立的work线程池

2.轮询拉取事件

3.支持批处理

4.TokenStore持久化（消费进度）

5.支持重试策略、死信队列



假设我的group，配置了10个线程，batchsize=100，意味着：这个group的所有事件会分为10个片，每一个segment有一个线程处理，每个线程最多从对应的segment中拉去100个事件。

1.可以**自定义事件如何分片**

2.**每个 Segment 独立维护自己的 Token**

3.每个 Segment 的 专属线程负责在处理完一个 batch 后，将该 batch 的最后一个事件位置提交到 `TokenStore`

4.所有 handler 的执行结果（成功/失败共同决定该 batch 的 Token 是否提交



query：可配置同步/异步。

1.同步

2.同步-延迟响应：future，伪异步

3.真异步-订阅式：客户端**订阅更新流**，服务端在数据变化时**回调客户端**。需要服务端更新数据时回调！（了解个大概，对流式处理还不太熟悉）



### handler的注册与路由

注册：项目中是配置中显式指定。

路由：显示注解 or 第一个参数类型







### UnitOfWork

每一次消息处理（Command/Event/Query），都在一个 `UnitOfWork` 中执行；它定义了“一次业务操作”的完整生命周期。

看看AI怎么说：

> 这是一个触及 Axon **核心运行时引擎**的问题——`UnitOfWork`（工作单元）是 Axon 框架中**最底层、却最关键**的抽象之一，它是**事务边界、消息流上下文、异常传播、资源协调**的统一载体。
>
> 简单说：**每一次消息处理（Command/Event/Query），都在一个 `UnitOfWork` 中执行；它定义了“一次业务操作”的完整生命周期**。
>
> 下面我将从 **概念本质 → 核心职责 → 生命周期 → 实现机制 → 实战价值** 五层递进，助你真正掌握这个“隐形指挥官”。
>
> ---
>
> ## 一、本质：什么是 UnitOfWork？
>
> > 📌 **定义**：  
> > `UnitOfWork` 是一次**原子性消息处理过程**的运行时上下文，它封装了：
> > - 当前处理的消息（Command/Event/Query）；
> > - 该消息引发的**所有副作用**（新事件、查询更新、DB 操作）；
> > - 执行过程中的**元数据、资源、异常状态**；
> > - **提交/回滚的决策点**。
>
> ### ✅ 类比理解
> | 场景       | UnitOfWork 类比                                 |
> | ---------- | ----------------------------------------------- |
> | 数据库事务 | `@Transactional` 方法的执行上下文               |
> | HTTP 请求  | `HttpServletRequest` + `FilterChain` 的生命周期 |
> | 函数式编程 | `IO[Result]` 的执行环境（含副作用收集）         |
>
> > ⚠️ 关键区别：**UnitOfWork 不等于数据库事务**！  
> > 它是**更高层的业务事务**，可包含：  
> > - 1 个 DB 事务 + N 个事件发布 + 1 个缓存更新 + ...  
> > - 甚至**跨多个 DB 事务**（Saga 场景）。
>
> ---
>
> ## 二、核心职责：UnitOfWork 做什么？
>
> ### 1️⃣ **消息上下文容器**
> - 存储当前消息及其元数据：
>   ```java
>   // 在 handler 中获取当前 UnitOfWork
>   @CommandHandler
>   public void handle(CreateOrderCommand cmd, UnitOfWork<CommandMessage<?>> uow) {
>       CommandMessage<?> message = uow.getMessage(); // ← 原始消息
>       MetaData metaData = message.getMetaData();    // ← 元数据（如 userId, traceId）
>       String userId = metaData.get("userId");
>   }
>   ```
>
> ### 2️⃣ **副作用收集器（Side-effect Collector）**
> - 收集本次处理产生的**所有新消息**：
>   | 操作         | 如何注册到 UnitOfWork                                  |
>   | ------------ | ------------------------------------------------------ |
>   | 发布事件     | `Aggregate.apply(event)` → 事件暂存于 uow              |
>   | 发送命令     | `commandGateway.send(...)` → 命令加入 uow 的待发送队列 |
>   | 发布查询更新 | `queryUpdateEmitter.emit(...)` → 更新加入 uow          |
>   
>   → **所有副作用暂存，待 uow.commit() 时统一提交**，保证原子性。
>
> ### 3️⃣ **事务协调者（Transaction Coordinator）**
> - 控制提交/回滚时机：
>   ```java
>   uow.onPrepareCommit(u -> {
>       // 提交前：flush DB session、校验最终状态
>       entityManager.flush();
>   });
>         
>   uow.onCommit(u -> {
>       // 提交后：发通知、清理缓存
>       auditLogService.log(u.getMessage());
>   });
>         
>   uow.onRollback(u -> {
>       // 回滚时：补偿操作（慎用！）
>       compensatingService.undoReservation(u.getMessage());
>   });
>   ```
>
> ### 4️⃣ **异常传播控制器**
> - 统一异常处理链：
>   ```java
>   uow.onRollback(u -> {
>       Exception cause = u.getExecutionException(); // ← 获取原始异常
>       if (cause instanceof InsufficientStockException) {
>           // 转为业务友好错误
>           throw new BusinessException("库存不足", cause);
>       }
>   });
>   ```
>   → 避免 handler 内散落 `try-catch`，实现**集中式异常策略**。
>
> ### 5️⃣ **资源绑定器（Resource Binder）**
> - 关联线程局部资源（如 DB 连接、Trace 上下文）：
>   ```java
>   // Spring 集成时，自动将 PlatformTransactionManager 绑定到 uow
>   uow.attachResource("entityManager", entityManager);
>   ```
>
> ---
>
> ## 三、生命周期：一次 Command 处理中的 UoW 演进
>
> ```mermaid
> sequenceDiagram
>     participant Client
>     participant CommandBus
>     participant UoW
>     participant Handler
>     participant EventStore
> 
>     Client->>CommandBus: send(CreateOrderCommand)
>     CommandBus->>UoW: start(command)
>     UoW->>Handler: invoke @CommandHandler
>     Handler->>UoW: apply(OrderCreatedEvent)  // 事件暂存
>     Handler->>UoW: return
>     UoW->>UoW: onPrepareCommit() → flush DB
>     UoW->>EventStore: persist events
>     UoW->>EventBus: publish events
>     UoW->>UoW: onCommit()
>     UoW-->>CommandBus: result
>     CommandBus-->>Client: success
>     alt 处理中抛出异常
>         Handler->>UoW: throw Exception
>         UoW->>UoW: onRollback() → 回滚 DB
>         UoW-->>CommandBus: Exception
>     end
> ```
>
> ### 关键阶段说明：
> | 阶段                | 触发时机                      | 典型操作                        |
> | ------------------- | ----------------------------- | ------------------------------- |
> | **`start`**         | 消息进入总线时                | 创建新 UoW 实例；绑定线程上下文 |
> | **`execution`**     | handler 方法执行中            | 收集事件、命令、更新            |
> | **`prepareCommit`** | handler 正常返回后            | DB flush、状态最终校验          |
> | **`commit`**        | 所有 prepare 成功后           | 持久化事件、广播消息、释放资源  |
> | **`rollback`**      | handler 抛异常或 prepare 失败 | DB rollback、清理暂存副作用     |
>
> > 🔑 **核心原则**：  
> > **副作用（事件/命令）的可见性 = UoW 的提交**。  
> > —— 在 `commit()` 前，外部完全看不到本次操作的影响。
>
> ---
>
> ## 四、实现机制：UoW 如何工作？（技术深潜）
>
> ### 1️⃣ **线程绑定：ThreadLocal 存储**
> ```java
> // org.axonframework.messaging.unitofwork.DefaultUnitOfWork
> private static final ThreadLocal<UnitOfWork<?>> CURRENT = new InheritableThreadLocal<>();
> 
> public static <T> UnitOfWork<T> startAndGet(Message<T> message) {
>     UnitOfWork<T> uow = new DefaultUnitOfWork<>(message);
>     CURRENT.set(uow); // ← 线程绑定
>     return uow;
> }
> ```
> → 保证同一线程内 `UnitOfWork.current()` 可获取当前上下文。
>
> ### 2️⃣ **阶段状态机**
> UoW 内部维护严格状态流转：
> ```java
> enum Phase { NOT_STARTED, STARTED, PREPARED_COMMIT, COMMITTED, ROLLED_BACK }
> // 禁止非法跳转：如 COMMITTED → PREPARED_COMMIT
> ```
>
> ### 3️⃣ **嵌套 UoW 支持（罕见但强大）**
> ```java
> // 外层 UoW
> UnitOfWork<?> outer = UnitOfWork.startAndGet(cmd1);
> outer.execute(() -> {
>     // 内层 UoW（独立提交）
>     UnitOfWork<?> inner = UnitOfWork.startAndGet(cmd2);
>     inner.execute(() -> { ... });
>     inner.commit(); // inner 提交，outer 仍可回滚
> });
> outer.commit();
> ```
> → 用于 Saga 中的补偿步骤等高级场景。
>
> ---
>
> ## 五、实战价值：为什么你需要关心 UoW？
>
> ### ✅ 1. **精准控制事务边界**
> 场景：订单创建需扣库存 + 冻结支付额度  
> → 将两者放入**同一个 UoW**：
> ```java
> @CommandHandler
> public void handle(CreateOrderCommand cmd) {
>     inventoryService.reserve(cmd.items());   // 扣库存（DB 更新）
>     paymentService.freeze(cmd.amount());     // 冻结额度（DB 更新）
>     apply(new OrderCreatedEvent(...));       // 生成事件
>     // ← 若任一失败，全部回滚（DB + 事件均不持久化）
> }
> ```
>
> ### ✅ 2. **实现跨技术栈原子性**
> ```java
> uow.onCommit(u -> {
>     // DB 提交后，才发邮件（避免 DB 成功但邮件失败导致状态不一致）
>     emailService.sendOrderConfirmation(orderId);
> });
> ```
> → **事件驱动 ≠ 放弃一致性**，UoW 让你优雅协调同步与异步操作。
>
> ### ✅ 3. **构建可追溯的审计日志**
> ```java
> uow.onCommit(u -> {
>     auditLog.info("Command {} by user {} committed, events: {}",
>         u.getMessage().getPayloadType().getSimpleName(),
>         u.getMessage().getMetaData().get("userId"),
>         u.getMessages(EventMessage.class).stream()
>           .map(m -> m.getPayloadType().getSimpleName())
>           .collect(Collectors.toList())
>     );
> });
> ```
>
> ### ✅ 4. **实现分布式追踪透传**
> ```java
> @Bean
> public MessageHandlerInterceptor<CommandMessage<?>> traceInterceptor() {
>     return (uow, chain) -> {
>         // 从消息元数据提取 TraceID，绑定到当前线程 MDC
>         String traceId = uow.getMessage().getMetaData().get("traceId", String.class);
>         try (var ignored = MDC.putCloseable("traceId", traceId)) {
>             return chain.proceed();
>         }
>     };
> }
> ```
> → **全程无需修改 handler 代码**，UoW 是统一接入点。
>
> ---
>
> ## 六、常见误区澄清
>
> | 误区                            | 正解                                                         |
> | ------------------------------- | ------------------------------------------------------------ |
> | “UoW = Spring `@Transactional`” | Spring 事务是 UoW 的**资源之一**；UoW 可协调多个事务（如 DB + Kafka） |
> | “UoW 只用于 Command”            | Event/Query 处理同样有独立 UoW（如 Projection 更新 DB）      |
> | “必须手动管理 UoW”              | **默认全自动**！仅高级场景需干预（如自定义 onCommit）        |
> | “UoW 会影响性能”                | 轻量级对象，开销≈一个 HashMap；收益远大于成本                |
>
> ---
>
> ## 七、高级技巧：UoW 的扩展点
>
> ### 1. **自定义 UoW 类型**
> ```java
> public class TenantAwareUnitOfWork extends DefaultUnitOfWork<CommandMessage<?>> {
>     private final String tenantId;
>     
>     public TenantAwareUnitOfWork(CommandMessage<?> msg) {
>         super(msg);
>         this.tenantId = msg.getMetaData().get("tenantId");
>     }
> }
> 
> // 替换默认工厂
> @Bean
> public UnitOfWorkFactory<CommandMessage<?>> unitOfWorkFactory() {
>     return msg -> new TenantAwareUnitOfWork(msg);
> }
> ```
>
> ### 2. **全局拦截所有 UoW**
> ```java
> @Component
> public class UowMonitor implements UnitOfWorkFactory<CommandMessage<?>> {
>     private final UnitOfWorkFactory<CommandMessage<?>> delegate;
> 
>     public UowMonitor(UnitOfWorkFactory<CommandMessage<?>> delegate) {
>         this.delegate = delegate;
>     }
> 
>     @Override
>     public UnitOfWork<CommandMessage<?>> createUnitOfWork(CommandMessage<?> msg) {
>         UnitOfWork<CommandMessage<?>> uow = delegate.createUnitOfWork(msg);
>         uow.onCommit(u -> metrics.counter("uow.commits").increment());
>         uow.onRollback(u -> metrics.counter("uow.rollbacks").increment());
>         return uow;
>     }
> }
> ```
>
> ---
>
> ## 总结：UnitOfWork 的哲学
>
> > 🔑 **UnitOfWork 是 Axon 对“业务操作原子性”的终极抽象**：  
> > - 它把**一次用户意图**（Command）、**一次状态变更通知**（Event）、**一次数据查询**（Query）  
> > - 封装为**有始有终、可追溯、可协调、可回滚**的独立宇宙。
>
> 当你看到：
> ```java
> @CommandHandler
> public void handle(CreateOrderCommand cmd) { ... }
> ```
> 背后是一个 `UnitOfWork` 在默默工作：
> - 为你开启事务；
> - 为你收集事件；
> - 为你拦截异常；
> - 为你提交一切；
> - 为你留下审计痕迹。
>
> 这才是**企业级事件驱动架构的坚实地基**。
>
> 如需，我可以提供：
> - UoW 与 Spring TransactionManager 的集成细节图；
> - 如何用 UoW 实现“逻辑删除”代替物理删除；
> - 在测试中 Mock UnitOfWork 的技巧。
>





## Message

元数据：默认给消息封装的context



拦截器链：

1.dispatch：消息发送前（Gateway -- Dispatch -- Bus）

2.handler：消息处理中（Bus -- HandlerIntercept -- Handler）



事务管理：

简单理解为集成在workunit中，系统中通过配置，command 默认开启事务。



异常控制流：

1.拦截器中处理异常（当前系统的配置，command & query）

2.TrackingEventProcessor的 Error Handling Strategy（Event）

```java
@Bean
public PropagatingErrorHandler eventErrorHandler() {
    return PropagatingErrorHandler.builder()
        // 乐观锁冲突 → 重试 5 次，指数退避
        .onError(OptimisticLockingException.class, 
                 (event, ex) -> ErrorHandlingStrategy.retry(5, 100, 2.0))
        
        // 业务异常 → 跳过，记录日志
        .onError(IllegalArgumentException.class, 
                 (event, ex) -> {
                     log.warn("Skip invalid event: {}", event, ex);
                     return ErrorHandlingStrategy.SKIP;
                 })
        
        // 未知异常 → 死信队列
        .onError(Exception.class,
                 (event, ex) -> {
                     deadLetterQueue.send(event, ex);
                     return ErrorHandlingStrategy.SKIP;
                 })
        .build();
}
```





对AXON的极简理解：
![image-20251220140705239](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220140705239.png)

# CQRS核心原语



## command

基础的结构：

![image-20251220142111444](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220142111444.png)

「在aggregate中，commandHandler一般负责业务规则校验，及决策并产生event。这个过程是纯函数式的，没有状态变更，也不会请求外部API，没有任何副作用：输入 = 当前状态 + 命令 → 输出 = 事件列表 / 异常。

eventHandler则是响应本aggregate产生的事件，修改状态，但必须要注意幂等（因为事件溯源涉及重放）」



### 匹配原则

1.依据handler的第一个参数的Class类型进行匹配

2.指定名称



### 线程模型

因为目前业务使用的SimpleCommandBus，故基于这个前提解释如下三个方法的线程模型

```java
1.<C, R> void send(C var1, CommandCallback<? super C, ? super R> var2);
2.<R> R sendAndWait(Object var1);
3.<R> CompletableFuture<R> send(Object var1);
```

总结：SimpleCommandBus 默认是 **同步、单线程模型**—— 所有命令处理都在 发送命令的线程中**串行**执行 handler！

```java
// 伪代码简化
protected void doDispatch(CommandMessage<?> command) {
    // 1. 找到对应的 handler
    CommandHandler handler = findHandler(command);
    
    // 2. 直接调用！—— 在当前线程执行
    Object result = handler.handle(command);  // ← 同步调用！
    
    // 3. 触发回调 / 设置 CF / 返回值
    callback.onSuccess(...);
}
```

![image-20251220150256537](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220150256537.png)



虽然给了异步API实际仍然都是同步执行的！







### commandBus

RetryScheduler 只限于DistributedCommandBus

commandBus可以有多种实现，这里只简单的罗列[more info](https://docs.axoniq.io/axon-framework-reference/4.12/axon-framework-commands/infrastructure/)

1. SimpleCommandBus
2. AsynchronousCommandBus
3. DisruptorCommandBus



![分布式命令总线的结构](https://docs.axoniq.io/axon-framework-reference/4.12/axon-framework-commands/_images/distributed-command-bus.png)





## query

![image-20251220152932414](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220152932414.png)



### 匹配

同command



### 调度

1. [Point-to-point queries](https://docs.axoniq.io/axon-framework-reference/4.12/queries/query-dispatchers/#point-to-point-queries),
2. [Scatter-gather queries](https://docs.axoniq.io/axon-framework-reference/4.12/queries/query-dispatchers/#scatter-gather-queries),
3. [Subscription queries](https://docs.axoniq.io/axon-framework-reference/4.12/queries/query-dispatchers/#subscription-queries),
4. [Streaming queries](https://docs.axoniq.io/axon-framework-reference/4.12/queries/query-dispatchers/#streaming-queries).



### 线程模型

这里暂且只讨论基础的点对点&分散收集，也都是基于系统SimpleQueryBus

1.点对点，同步阻塞，send与handler 同一个线程

2.分散收集：依旧是同步阻塞，同一个线程会先后调用所有匹配上的handler





## event



### 总览

对于Listener Instance，at most one handler会被调用。

event 需要区分aggregate与通用component中的handler。



事件的处理：

1.订阅式：推送给consumer

2.流式：从eventBus中去拉取。系统中用的流式，这里也重点介绍流式。



事件处理的结构：

![事件处理器和事件处理程序的组织](https://docs.axoniq.io/axon-framework-reference/4.12/events/_images/event-processors.png)



Axon 的事件处理模型是典型的 **管道-过滤器（Pipeline-Filter）架构**

```text
Event Store / EventBus
        ↓
┌───────────────────────┐
│  ③ Event Processor    │ ← 并发、分组、错误处理、监控
│  (Streaming / Subscribing) │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│  ② Processing Group   │ ← 逻辑分组、负载均衡单元
│  (e.g., "order-group") │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│  ① Event Handler       │ ← 业务逻辑（@EventHandler 方法）
│  (POJO with @Component) │
└───────────────────────┘
```



1.event handler:纯业务代码，不关心并发、重试、分片等基础设施问题

2.group：功能相关的handler聚合成的一个处理单元，负载均衡、并行处理、错误隔离的最小单位

![image-20251220165815863](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220165815863.png)

3.event Processor

![image-20251220170702571](https://cdn.jsdelivr.net/gh/liaozk-wiki/md_img/md/image-20251220170702571.png)



eventStore 存储event

tokenTrace跟踪segment中事件的处理

二者在系统中均会持久化到数据库。



event可以配置多数据源。



### 线程模型

在 Streaming Event Processor 模式下：

所有 `@EventHandler` 的执行线程与事件发布线程（`eventBus.publish(...)` 的线程）**一定不一致**（**异步解耦**）；

但不同 eventHandler 之间是否同线程，取决于它们是否属于**同一 segment**。



# 系统中的实践

性能监控？

幂等管理：元数据幂等id是怎么来的？如何设置的？

raven-sentry 错误处理

默认的事务、事件序列化、事件存储

commandbus：

事务

性能监控？

dispatch拦截：日志+valid

handler拦截：日志+sentry+幂等

rollback



querybus：

eventbus：





注册所有handler：如何实现？injector 边界？何时触发的扫描？

这里的注册，比较有意思：

1. injector 中绑定了UwModle

2. UwModel中使用了@ProvidesIntoSet （等价于Multibinder.newSetBinder（...）.addBinding().toProvider（...），表示需要将结果放到set中去）@自定义注解（自定义注解中@BindingAnnotation表示自定义注解是用于区分同类型的不同绑定）

3. 最终通过Key<Set<Function<Configuration, Object>>> aKey = Key._get_(Set.class, annotationType)        .ofType(new TypeLiteral<Set<Function<Configuration, Object>>>() {        });  及 injector.getInstance(aKey); 获取所有的注解标注的返回值

4. 最后依次将结果注册到config中去，实现了注册所有handler类

5. 至于类中的具体方法，则遵循axon的原则，选择具体调用哪一个了

   

接下来是最有意思的：配置eventHandler！



系统有跨jvm的axon调用吗？可以使用axon调用替换掉http或rpc吗？已知系统有通过mq转发event到不同的服务，然后各自的服务的eventHandler 消费event。（2025-12-20 尚未找到代码点）



eventProcessing：

追踪式处理：拉模式，独立线程定期拉取event。



```java
private Consumer<EventProcessingConfigurer> configEventProcessing(
            Set<Function<Configuration, Object>> eventListeners,
            Set<Pair<String, Function<Configuration, TrackingEventProcessorConfiguration>>> listenerParallelConfigs) {
        LOGGER.debug(Env.NBC_POLICY_TRANSMIT_THREAD_COUNT + ":" +
                Env.NBC_POLICY_TRANSMIT_SEGMENT_COUNT + ":" +
                Env.NBC_POLICY_TRANSMIT_BATCH_SIZE);
        LOGGER.info("######### configEventProcessing, eventListeners.size: {}, listenerParallelConfigs.size: {}",
                eventListeners.size(),
                listenerParallelConfigs.size());
        return eventProcessingConfigurer -> {
            eventProcessingConfigurer
            // 已替换成注解@EventProcessorParallelConfig
//                    .registerTrackingEventProcessorConfiguration(
//                            "com.picc.nbc.application.event.domainevent.handler.demoprocessor",
//                            c -> TrackingEventProcessorConfiguration
//                                    .forParallelProcessing(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_THREAD_COUNT))
//                                    .andInitialSegmentsCount(
//                                            Integer.valueOf(Env.NBC_POLICY_TRANSMIT_SEGMENT_COUNT))
//                                    .andBatchSize(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_BATCH_SIZE))
//                                    .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
//                    .registerTrackingEventProcessorConfiguration(
//                            "com.picc.nbc.application.event.domainevent.handler.policytransmitfailprocessor",
//                            c -> TrackingEventProcessorConfiguration
//                                    .forParallelProcessing(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_FAIL_THREAD_COUNT))
//                                    .andInitialSegmentsCount(
//                                            Integer.valueOf(Env.NBC_POLICY_TRANSMIT_FAIL_SEGMENT_COUNT))
//                                    .andBatchSize(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_FAIL_BATCH_SIZE))
//                                    .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
//                    .registerTrackingEventProcessorConfiguration(
//                            "com.picc.nbc.application.event.domainevent.handler.policytransmit",
//                            c -> TrackingEventProcessorConfiguration
//                                    .forParallelProcessing(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_THREAD_COUNT))
//                                    .andInitialSegmentsCount(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_SEGMENT_COUNT))
//                                    .andBatchSize(Integer.valueOf(Env.NBC_POLICY_TRANSMIT_BATCH_SIZE))
//                                    .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
                    .registerTrackingEventProcessorConfiguration(c -> TrackingEventProcessorConfiguration
                            .forSingleThreadedProcessing()
                            .andBatchSize(EVENT_BUS_TRACKING_BATCH_SIZE)
                            .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
            // 已替换成注解@EventProcessorParallelConfig
//                    .registerTrackingEventProcessorConfiguration(
//                            "cbs.pa.application.eventlistener.newbiz.approval.btunitevent",
//                            c -> TrackingEventProcessorConfiguration
//                                    .forParallelProcessing(4)
//                                    .andInitialSegmentsCount(4)
//                                    .andBatchSize(100)
//                                    .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
//                    .registerTrackingEventProcessorConfiguration(
//                            "cbs.pa.application.eventlistener.newbiz.approval.unitidevent",
//                            c -> TrackingEventProcessorConfiguration
//                                    .forParallelProcessing(4)
//                                    .andInitialSegmentsCount(4)
//                                    .andBatchSize(100)
//                                    .andInitialTrackingToken(StreamableMessageSource::createHeadToken))
                    .registerDefaultErrorHandler(c -> new SentryPropagatingErrorHandler(raven))
                    .registerDefaultListenerInvocationErrorHandler(c -> new SentryPropagatingErrorHandler(raven))
                    .registerDefaultHandlerInterceptor(
                            (c, n) -> new LoggingHandlerInterceptor<>())
                    .registerDefaultHandlerInterceptor((c, n) -> new EventProcessorLogInterceptor<>(saveEventProcessor,
                            () -> Preconditions.checkArgument(CurrentTransaction.isActive())))
                    .registerDefaultHandlerInterceptor(
                            (c, n) -> new EventProcessIdempotencyInterceptor<>(idempotencyManager,
                                    new JsonXMLSerializer().getJsonSerializer(),
                                    () -> Preconditions.checkArgument(CurrentTransaction.isActive())));
            for (Pair<String, Function<Configuration, TrackingEventProcessorConfiguration>> parallelConfig : listenerParallelConfigs) {
                eventProcessingConfigurer.registerTrackingEventProcessorConfiguration(parallelConfig.getLeft(),
                        parallelConfig.getRight());
            }
            for (Function<Configuration, Object> listener : eventListeners) {
                eventProcessingConfigurer.registerEventHandler(listener);
                /*
                 * if (!PROD_ENV) {
                 * eventProcessingConfigurer.registerRollbackConfiguration(
                 * listener.apply(config).getClass().getPackageName(), c ->
                 * RollbackConfigurationType.NEVER); }
                 */
            }
//已经迁移到cbs.claim.delivery.di.ReportEventHandlerModule.provideRegisterCopyPolicyCalcResultsListenerConfig
//            for (ClaimEventParallelProcessingEnum parallelProcessingEnum : ClaimEventParallelProcessingEnum.values()) {
//                eventProcessingConfigurer.registerTrackingEventProcessorConfiguration(
//                        parallelProcessingEnum.getProcessorName(),
//                        c -> TrackingEventProcessorConfiguration
//                                .forParallelProcessing(parallelProcessingEnum.getThreadCount())
//                                .andInitialSegmentsCount(parallelProcessingEnum.getSegmentsSize())
//                                .andBatchSize(parallelProcessingEnum.getBatchSize())
//                                .andInitialTrackingToken(StreamableMessageSource::createHeadToken));
//            }
        };
    }
```

eventProcessingConfigurer.registerTrackingEventProcessorConfiguration:

可以给不同的group注册不同的配置，默认是按照packageName 作为groupName，所以Pair的left必须是packageName。Pair的right则是配置，track 默认是单线程的，配置指定了多线程就会变成多线程处理。







event Porcessor 负责处理从消息队列/事件存储中拉取事件，并分发给eventHandler。

AxonErrorHandler的两个方法：

1. handleError：eventProcessor 出错了调用这个

2. onError：单个eventHandler出错了调用这个



注册事件处理：

1.注册默认的tracking事务处理

2.注册eventProcessor 的 errorHandler 与 eventHandler 的 errorHandler

3.添加defaultHandlerInterceptor

    3.1：输出message & returnValue

    3.2：EventProcessorLogInterceptor ：事务check，幂等，记录event处理结果，通过对user Transaction的wrap 实现与此处TranMonitor的协作。

    3.3：幂等处理（3.2中也有幂等，也有事务check，为什么这里也有？为什么还要额外记录另外一张表？）--一个是技术上的幂等，一个是业务上的幂等，这是两个完全不同的概念。

4.processorConfig

5.注册handler



在axon系统配置层面其实并没有注册任何重试机制！





整个流程基本清晰起来了：

frist：整个代码虽然杂糅在一起，但实际上是有好几个服务的，每个服务都有一个自己的axon配置与启动。

second：axon中所有handler的注册都依赖于guice的injector，通过不同的model控制边界，通过注解统一获取handler。



完整的配置流程：

1. 注册默认的事务管理

2. 注册默认的序列化

3. 注册commandBus

   1. 事务、监控、dispatchInterceptor（logging+valid）、handlerInterceptor（logging+sentry+幂等）、事务回滚配置

4. 注册queryBus

   1. 事务、监控、dispatchInterceptor（logging+valid）hanlderInterceptor（logging+sentry）

5. 注册eventBus

   1. 监控+dispatchLog

6. 注册eventStore

7. 注册连接provider

8. 注册gateway provider



注册各类handler（没有event的）：

0. 结合前面的guice，外面new finder时就初始化了所有的handler

1. 分别注册command handler

2. 分别注册query handler



配置事件处理：

1. 注册tokenStore

2. 注册propagate handler

3. 定制事件处理

   1. 默认的单线程处理

   2. processor errorHandler

   3. single hanlder errorHandler

   4. 三个handler拦截器

      1. log

      2. 事件处理（技术上幂等）

      3. 幂等（业务上幂等）

   5. 注册processor 配置（实现按照packageName 自定义多线程事务处理，同一个package属于同一个group，一个group会按照配置配置多线程，拉取事件然后调用内部的handler）

   6. 注册所有的eventHandler

4. 注册sagastore





done！



现在距离之前的任务目标，还剩下：

失败重试机制在哪里？(command与event有，其中event一般放在errorHandler，command则是拥有RetryScheduler)

前置调用核保会用到axon吗？

如何测试出合理的配置？



> 有个问题可以调查下呢，下次汇报的时候讲下。就是契约中心上周上线一个消息失败的重试机制，感觉是生效了，就是没有调查具体的数据；学习目的是熟悉下axon体系，契约中心与核心的交互，这个重试机制是否可以进一步优化
> 具体这个重试机制在哪可以咨询下国成哈
> 如果没有生产权限可以在测试环境写好脚本测试，然后发给国成执行下
> 重试机制是调核心接口失败重试4次，每次追加X分钟的间隔
> 这个X分钟是否合适



为了这盘醋，包了这顿饺子🤣



最后找到了重试机制的代码点：没有使用event的配置，而是在eventHandler处通过try-catch捕获异常就重新发布event，同时在metaData中增加次数，当次数达到指定次数后，终止重试流程。

1.如何设置最优参数？--如何制定压测计划？

2.这里的整个重试机制，更大的背景其实是田处要求，对所有的失败要有兜底机制，不能失败了就放在哪里。

3.听说有事件没有处理，丢失了？why？通过前面，event发布可是会持久化，event消费也是有进度表持久化的。











