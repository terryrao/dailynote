# spring aop 相关问题

1. @pointcout execution 子类方法能代理，父类方法不能代理的问题

这个时候可以将父类所在的路径加到另一个 `@pointcut` 中，代码如下：

```java
@Aspect
@Component
public class DemoAspect{
    /**
    * 代理子类所有公开方法
    */
    @Pointcut("execution(public * com.demo.repository.impl..*.*(..))")
    public void suClass() {
    }
     /**
    * 代理父类所有公开方法
    */
    @Pointcut("execution(public * com.demo.Repository.*(..))")
    public void repositoryBase() {}
    
    /**
 	 * 将两个切面都加入进来
 	 */
    @AfterReturning(returning = "rvt", value = "autoWired()|| repositoryBase()")
    public Object after(JoinPoint joinPoint, Object rvt) {
      	 System.out.println("do something")
        return rvt;
    }
    
}

```

