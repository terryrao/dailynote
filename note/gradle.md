# gradle java 项目简易说明

基于 gradle 5.5.1 版本
## 1. 单项目设置 build.gradle 

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
定义项目 version、group:
```gradle
group = 'com.example'
version = '0.0.1-SNAPSHOT'
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
添加仓库，引入镜像阿里镜像：
```gradle
repositories {
    mavenLocal() //本地仓库
    //阿里镜像
    maven {
        name "aliyunmaven"
        url "http://maven.aliyun.com/nexus/content/groups/public/"
    }
    mavenCentral()
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
完整示例如下：
```gradle 
plugins {
  id 'org.springframework.boot' version '2.1.7.RELEASE'
  id 'io.spring.dependency-management' version '1.0.7.RELEASE'
  id 'java'
}
ext {
    jmockitVersion = 1.4.7
}
group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = JavaVersion.VERSION_1_8
targetCompatibility = JavaVersion.VERSION_1_8
configurations {
  developmentOnly
  runtimeClasspath {
    extendsFrom developmentOnly
  }
  compileOnly {
    extendsFrom annotationProcessor
  }
}
repositories {
    mavenLocal() //本地仓库
    //阿里镜像
    maven {
        name "aliyunmaven"
        url "http://maven.aliyun.com/nexus/content/groups/public/"
    }
    mavenCentral()
}
ext {
  set('springCloudVersion', "Greenwich.SR2")
}
dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-freemarker'
  implementation 'org.springframework.boot:spring-boot-starter-web'
  implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'
  compileOnly 'org.projectlombok:lombok'
  developmentOnly 'org.springframework.boot:spring-boot-devtools'
  annotationProcessor 'org.projectlombok:lombok'
  testImplementation 'org.springframework.boot:spring-boot-starter-test'
  testImplementation "org.jmockit:jmockit:${jmockitVersion}"`	1
}
dependencyManagement {
  imports {
    mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
  }
}
test { 
 	jvmArgs "-javaagent:${classpath.find { it.name.contains("jmockit") }.absolutePath}"
}

```
快捷生成 spring 项目相关 build.gradle 网址：https://start.spring.io/

## 2. FAQ
1. test jmockit 相关单例时，无法通过。

有两种情况 ：
1） 当出现初始化异常则需要在 `build.gradle` 中加入以下代码：
```gradle
test { 
 	jvmArgs "-javaagent:${classpath.find { it.name.contains("jmockit") }.absolutePath}"
}
```
2)   如果上述代码加入后 ide 仍然报错，而命令行使用 `gradle build` 正常，则需要在 ide 的 `unitRunner` 模板里 vm options 添加 `-javaagent=/.../path/jmockit.jar` 参数。
3)   如果上述步骤之后，启动正常但是报如 nullPointerException  之类的异常，如果在排除代码的异常之后，命令行没有问题 ，可以查看是一下启动 gradle 的 jdk 版本怎么项目的 jdk 版本是不是一致的

2. 如果插件不是标准 maven 库和 gradle plugin 库中的插件，需要在当前 `build.gradle`所在目录里加入 `init.gradle` 文件，加入 repositories ：
```gradle
repositories {
    //阿里镜像或者指定的镜像
    maven {
        name "aliyunmaven"
        url "http://maven.aliyun.com/nexus/content/groups/public/"
    }
}
```



