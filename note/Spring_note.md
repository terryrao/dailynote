# Spring 笔记

## Core Technologies

### 1. IoC Container

`org.springframework.beans`  和 `org.springframework.context` 是 IoC 容器的基础包。

`ApplicationContext` 是 `BeanFactory` 的子接口，它增加了以下功能：

1. Easier integration with Spring’s AOP features 更加容易与 Spring 的 AOP 功能集成
2. Message resource handling (for use in internationalization) 消息资源处理(国际化)
3. Event publication  事件发布
4. Application-layer specific contexts such as the `WebApplicationContext` for use in web applications 提供应用层特定的 contexts 如 `WebApplicationContext` 是在 Web 应用里使用的。

#### dependencies

##### Constructor argurment name 



要想使 constructor argument name 工作，必须开启 debug 标识，如果不愿开启，则需要 jdk 的 `@ConstructorProperties` 注解指定构造器参数的名字。

 基于 constructor 或者 基于 setter 的 DI？



既然可以混用 constructor-based 和 setter-based，那么将构造器用于强制依赖，将 setter 方法用于可选依赖是一个可靠的经验法则。

Spring 团队推荐使用 构造器依赖注入，因此它可以实现不变对象，保证依赖对象不会为空。更进一步，基于构造器注入的组件总能给客户端代码返回已经初始化完成的对象。当然，有一点副作用是，过多的构造器参数有一些坏味道，这也意味着你的类是否存在过多的职责，需要要重构？

##### Circular dependencies

循环依赖的解决方法：可以将一些类的注入方式由构造器注入改为 setter 注入



#### Autowiring Collaborators

autowiring 的四种模式

| Mode        | 解释                                             |
| :---------- | ------------------------------------------------ |
| no          | (默认)不自动装配，Bean 引用必须定义在 ref 引用里 |
| byName      | 根据属性的名称装配                               |
| byType      | 根据属性的类型状态                               |
| Constructor | 类型于 byType 但是作用于构造器参数               |

#### Bean Scope

| Scope       | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| Singleton   | 一个 IoC 只有一个该 bean 的实例                              |
| prototype   | 该 bean 有任意多个实例                                       |
| request     | 每个 http 都有一个实例，只限于 Web应用的 ApplicationContext  |
| session     | 每个 httpSession 生命周期有一个 bean 实例 ，只限于 web 应用的 ApplicationContext |
| application | 每一个ServletContext 一个  bean 实例，只限于 web 应用的 ApplicationContext |
| websocket   | 每一个 webSocket 生命周期一个 bean 实例，只限于 web 应用的 Application |

自定义 Scope 需要实现 `org.springframework.beans.factory.config.Scope` 接口

#### 定制化 bean

Spring 提供了许多接口用来定制 bean：

1. Lifecycle Callbacks
2. ApplicationContextAware and BeanNameAware
3. Other Aware Interfaces

##### 1.Lifecycle Callbacks 

要与容器的 bean 周期管理交互，你可以实现 `InitializingBean` 和 `DisposableBean` 接口。容器为前者调用  `afterPropertiesSet()` ，为后者调用  `destory()` ，让 bean 在初始化和销毁时执行某些操作。

JSR 250 `@PostConstuct`  和 `@PreDesotry` 通常情况下是 lifecycle Callbacks 的最佳实践。不推荐使用 `InitializingBean` 和`DisposableBean` 会与 spring 容器耦合。

##### 2. ApplicationContextAware and BeanAware



#### Container Extension Points

##### BeanPostProcessor 

`BeanPostProcessor` 接口定义的回调方法可以自定义自己的初始化逻辑。

