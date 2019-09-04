# AXON 

## 1. CQRS  架构

![CQRS 架构图](pic/axon_architecture-overview.png)

## 2. 基本概念：

### CQRS

QRS 是查询职责分离模式(Command Query Responsibility Segregation)的缩写。将应用程序分为两部分：命令端(Command) 和查询端 (Query)。命令端处理程序创建，更新和删除请求，并在数据更改时发出事件。查询端通过执行查询来处理查询，并且通过订阅数据更改时发出的事件流而保持最新。CQRS使用分离的接口将数据查询操作(Queries) 和数据修改操作(Commands) 分离开来，这也意味着在查询和更新过程中使用的数据模型也是不一样的。这样读和写逻辑就隔离开来了。

### EVENT SOURCING

事件溯源：

### cmmand

通常是一对象，包装一个命令需要的数据

### command Bus 

接收 command  并将它们路由给  command handler。每个 command hanlder 负责处理一个指定类型的 command 。

command handler  可以在 command bus 上订阅和发布特定类型的 command 。 一种类型的命令只能被一种 handler 订阅。

在某些情况下，也可以执行操作逻辑而不管实际的命令类型，如验证、日志或认证。

### command handler 

负责处理 command ,每个 command 只负责处理一种类型的 command 。

### repository 

所的有聚合对象都需要通过 repository 来存储和读取，聚合对象状态的改变由 event store 来存储。

### aggregate 

在 DDD 的设计中，这是 Aggregate 的标准使用方式：

1. Aggregate 只能通过 repository 装载
2. repository 通常喜欢根据 id 属性来装载 Aggregate
3. Aggregate 不是简单的VO，而是领域对象，不仅仅保持自身的数据和状态，而且有业务方法来实现业务逻辑

axon 中的 Aggregate 不能直接在业务方法中简单的操作自身的状态，即不能直接通过 `this.balanceInCents=***;` 这种方式来修改状态。而是要发送一个 Domain Event 给 Event Bus，然后自己再定义对应的 Event Handler 来处理这个 Domain Event：

### event

这个 Event 指 DDD 中的 Domain Event，按照 DDD 的设计，Aggregate 在状态变更时需要发送 Domain Event 来通知系统中的其他组件（尤其是 Aggregate 自己也不知道到底哪些组件对自己的这个状态变更感兴趣）。

###　Event Bus

Event Bus 将事件分派给所有感兴趣的 Event Listener 。可以是同步或者异步。异步事件分派容许命令执行返回并将控制交还给用户，而事件在后台被分派和处理。不需要等待事件处理完成可以让应用更具响应性。另一方面，同步事件处理更简单，也更容易理解。 同步处理也允许几个Event Listener 处理同一事务中的事件。

### Event Handler

Event Handler 接收并处理事件。

一些 Handler 将更新用于查询的数据源，而另外一些发送消息到外部系统。

你可能会注意到，command handle 完全不知道哪些组件会对他们做出的变更感兴趣。这意味着可以以非侵入性的方式来用新功能扩展应用程序。所有需要做的只是添加另一个 Event Handler。事件耦合了应用程序中的所有组件。



### Event Store

Event Store 提供机制，在底层 event storage 中开启流。

## 3. 重要注解

### 1） `@Aggragate`

```java
@Target({ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Component
@Scope("prototype")
@AggregateRoot
public @interface Aggregate 
```



本质上是一个 spring 的 component 。`CommandGateWay` 调用命令时需要它。

### 2） `@AggregateIdentifier`

标识字段是实体的唯一标识



### 3） `@CommandHandler`

标注对象或者方法做为一个 `CommandHandler`  ，使用 `AnnotationCommandHandlerAdapter` 去订阅 `command bus`。

### 4） `@AggregateMember`

标注在 聚合根里的实体属性上，当聚合根触发一个事件时，首先触发聚合根的事件，然后传播到 `AggregateMember` 中。(When an entity (including the aggregate root) applies an event, it is handled by the aggregate root first, and then bubbles down through every `@AggregateMember` annotated field to **all** its containing child entities:)

```java
import org.axonframework.commandhandling.CommandHandler;
import org.axonframework.modelling.command.AggregateIdentifier;
import org.axonframework.modelling.command.AggregateMember;
import org.axonframework.modelling.command.EntityId;

import static org.axonframework.modelling.command.AggregateLifecycle.apply;

public class GiftCard {

    @AggregateIdentifier
    private String id;
    @AggregateMember
    private List<GiftCardTransaction> transactions = new ArrayList<>();

    @CommandHandler
    public void handle(RedeemCardCommand cmd) {
        // Some decision making logic
        apply(new CardRedeemedEvent(id, cmd.getTransactionId(), cmd.getAmount()));
    }

    @EventSourcingHandler
    public void on(CardRedeemedEvent evt) {
        // 1.
        transactions.add(new GiftCardTransaction(evt.getTransactionId(), evt.getAmount()));
    } 

    // omitted constructors, command and event sourcing handlers 
}

public class GiftCardTransaction {

    @EntityId
    private String transactionId;

    private int transactionValue;
    private boolean reimbursed = false;

    public GiftCardTransaction(String transactionId, int transactionValue) {
        this.transactionId = transactionId;
        this.transactionValue = transactionValue;
    }

    @CommandHandler
    public void handle(ReimburseCardCommand cmd) {
        if (reimbursed) {
            throw new IllegalStateException("Transaction already reimbursed");
        }
        apply(new CardReimbursedEvent(cmd.getCardId(), transactionId, transactionValue));
    }

    @EventSourcingHandler
    public void on(CardReimbursedEvent event) {
        // 2.
        if (transactionId.equals(event.getTransactionId())) {
            reimbursed = true;
        }
    }

    // omitted getter and equals/hashCode
}
```



