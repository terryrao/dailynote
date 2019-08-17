# gradle java 项目简易说明

基于 gradle 5.5.1 版本

gradle 以项目和任务两个概念为中心，一个 `build.gradle`就是一个项目。构建一个项目需要执行一系列的 `task` 有些类似的 ant 概念。

与 `maven` 类似，在构建项目的时候可以引入各种各样的插件，帮助我们达成特定的目标，下面是引入一个 `spring boot` 插件开发 `spring boot` 项目:
```gradle
	//build.gradle
	// 引入插件，必须位于顶层
	plugins {
		id 'java' // 引入java语言插件
		id 'org.springframework.boot' version '2.1.7.RELEASE' //引入spring boot 插件 版本号为 2.1.7.RELEASE
	}
```
然后给项目配置代码的 jdk 编译版本：
```gradle
sourceCompatibility = JavaVersion.VERSION_1_8
targetCompatibility = JavaVersion.VERSION_1_8
```
配置字符编码:
```gradle
compileJava.options.encoding = "UTF-8"
compileTestJava.options.encoding = "UTF-8"
```
给版本号取别名，方便统一管理:
```gradle
ext {
	jmockitVersion = 1.4.7
}
```

添加依赖
```gradle
dependenices {
	implementation 'org.springframework.boot:spring-boot-start-web'
	testImplementation "org.jmockit:jmockit:${jmockitVersion}"
}
```

如果要添加 `spring cloud` 引入 bom 需要添加 `dependency-management` 插件：
```gradle
plugins {
  id "io.spring.dependency-management" version "1.0.8.RELEASE"
}
```
引入 `spring-cloud` 的 bom 依赖：
```gradle
dependencyManagement {
	mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
}
```
然后在 `dependencies` 中引入需要的依赖。
由于 jmockit 是使用 `-javaagent` 的方式启动的，所以需要在测试时添加 jvm 参数，将 jmockit 包添加到 jvm 启动参数中 :

```gradle 
 test { 
 	jvmArgs "-javaagent:${classpath.find { it.name.contains("jmockit") }.absolutePath}" 
 }

```


