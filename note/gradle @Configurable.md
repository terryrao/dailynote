# Gradle  Spring Boot 项目配置 @Configurable

我们都知道要使 一个对象里的 `@Autowired` 字段生效，这个对象必须被 spring 窗口托管，也就是这个对象必须是由 spring 容器 `new` 出来的，但是在有些情况下，我们需要自己去 `new ` 一个对象，也想 `@AutoWired`  属性生效，这个时候需要在类上加上注解 `@Configurable`：

```java
@Configurable
public class Customer {
    private int id;
    private String userName;
    @Autowired
    private CustomerRepository customerRepository;
}
```

然后启动起来，`new Customer()`，你会发现 这个 `customerRepository`  依然是空，这个 `@Configurable` 没有生效。

想要这个 `@configurable` 起作用，首先需要在 启动类里添加两个注解：

```java
@EnableLoadTimeWeaving
@EnableSpringConfigured
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

然后在 `build.gradle` 中引入两个 jar 包 :

```gradle
dependencies {
	runtimeOnly "org.aspectj:aspectjweaver:1.9.4"
	runtimeOnly "org.springframework:spring-instrument:5.1.9.RELEASE"
}

//跑 test 必须要加下以下语句
test {
	 def  aspectjweaver = classpath.find { it.name.contains("aspectjweaver") }.absolutePath
    def  spring_instrument = classpath.find { it.name.contains("spring-instrument") }.absolutePath
     jvmArgs = [ "-javaagent:${spring_instrument}"
               , "-javaagent:${aspectjweaver}"
    ]

}

```

这个时候再启动，会报需要在启动时加入 `spring-instrument` 的提示，所以要在启动的 `vm.options` 里加入 `aspectjweaver` 和 `spring-instrument` 两个包的路径：

```bash
-javaagent:"...\modules-2\files-2.1\org.aspectj\aspectjweaver\1.9.4\9205229878f3d62fbd3a32a0fb6be2d6ad8589a9\aspectjweaver-1.9.4.jar" 
-javaagent:"...\modules-2\files-2.1\org.springframework\spring-instrument\5.1.9.RELEASE\f60d26957cbb8bd30d2a19b90ad0b3652a27b2d\spring-instrument-5.1.9.RELEASE.jar"
```

这个时候启动可能会报 `warning javax.* types are not being woven because the weaver option '-Xset:weaveJavaxPackages=true' has not been specified` 的错误，然后 `weaving` 失败的问题，可以在

`resources\META-INF` 加入 `aop.xml` 文件：

```xml
<!DOCTYPE aspectj PUBLIC "-//AspectJ//DTD//EN" "http://www.eclipse.org/aspectj/dtd/aspectj.dtd">
<aspectj>
    <weaver options="-Xreweavable -showWeaveInfo">
        <!-- only weave classes with @Configurable interface -->
        <include within="@org.springframework.beans.factory.annotation.Configurable *"/>
    </weaver>
</aspectj>
```

最后，本地机器测试成功。