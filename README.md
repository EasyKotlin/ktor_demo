Ktor:  Kotlin Web后端框架  Web backend framework for Kotlin  快速开始入门
===

# Ktor 简介

Ktor 是一个用于在 Kotlin 中快速创建 web 应用程序的框架。

```kotlin
import org.jetbrains.ktor.netty.*
import org.jetbrains.ktor.routing.*
import org.jetbrains.ktor.application.*
import org.jetbrains.ktor.host.*
import org.jetbrains.ktor.http.*
import org.jetbrains.ktor.response.*

fun main(args: Array<String>) {
    embeddedServer(Netty, 8080) {
        routing {
            get("/") {
                call.respondText("Hello, world!", ContentType.Text.Html)
            }
        }
    }.start(wait = true)
}

```


# 创建工程

首先使用 IDEA 创建标准 Gradle+Kotlin 工程。

然后分别添加：

BlogApp.kt
logback.xml

目录结构如下：

```
$ tree
.
├── build.gradle
├── settings.gradle
└── src
    ├── main
    │   ├── java
    │   ├── kotlin
    │   │   └── com
    │   │       └── easy
    │   │           └── kotlin
    │   │               └── BlogApp.kt
    │   └── resources
    │       └── logback.xml
    └── test
        ├── java
        ├── kotlin
        └── resources

12 directories, 4 files

```

# 在build.gradle中配置 Ktor 依赖

```groovy
group 'com.easy.kotlin'
version '1.0-SNAPSHOT'

buildscript {
    ext.kotlin_version = '1.1.3-2'
    ext.ktor_version = '0.4.0' // ktor 版本

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

apply plugin: 'java'
apply plugin: 'kotlin'
apply plugin: 'application' // application plugin

mainClassName = 'com.easy.kotlin.BlogAppKt'  // main class

sourceCompatibility = 1.8

repositories {
    mavenCentral()
    maven { url "http://dl.bintray.com/kotlin/ktor" }
    maven { url "https://dl.bintray.com/kotlin/kotlinx" }
}

dependencies {
    ...
    compile "org.jetbrains.ktor:ktor-core:$ktor_version"
    compile "org.jetbrains.ktor:ktor-netty:$ktor_version"

    compile "ch.qos.logback:logback-classic:1.2.1"
}


kotlin {
    experimental {
        coroutines "enable"
    }
}

```


其中，

    compile "org.jetbrains.ktor:ktor-core:$ktor_version"
    compile "org.jetbrains.ktor:ktor-netty:$ktor_version"


是 ktor 核心依赖。


# BlogApp.kt

```kotlin
package com.easy.kotlin


import org.jetbrains.ktor.application.Application
import org.jetbrains.ktor.application.install
import org.jetbrains.ktor.features.CallLogging
import org.jetbrains.ktor.features.DefaultHeaders
import org.jetbrains.ktor.host.embeddedServer
import org.jetbrains.ktor.http.ContentType
import org.jetbrains.ktor.netty.Netty
import org.jetbrains.ktor.response.respondText
import org.jetbrains.ktor.routing.Routing
import org.jetbrains.ktor.routing.get
import java.util.*

fun Application.module() {
    install(DefaultHeaders)
    install(CallLogging)
    install(Routing) {
        get("/") {
            var html = "<li><a href = 'hello'>hello</a></li>"
            html += "<li><a href = 'now'>now</a></li>"
            call.respondText(html, ContentType.Text.Html)
        }

        get("/hello") {
            call.respondText("Hello, Ktor !", ContentType.Text.Html)
        }

        get("/now") {
            call.respondText("Now time is : ${Date()}", ContentType.Text.Html)
        }
    }
}

fun main(args: Array<String>) {
    embeddedServer(Netty, 8080, watchPaths = listOf("BlogAppKt"), module = Application::module).start()
}

```

# logback.xml

```xml
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{YYYY-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="trace">
        <appender-ref ref="STDOUT"/>
    </root>

    <logger name="org.eclipse.jetty" level="INFO"/>
    <logger name="io.netty" level="INFO"/>

</configuration>

```

# 运行结果

启动应用：


![螢幕快照 2017-09-05 22.42.29.png](http://upload-images.jianshu.io/upload_images/1233356-348139fd802d2e17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




输出日志：

```
22:40:44: Executing external task 'run'...
:compileKotlin
Using kotlin incremental compilation
:compileJava NO-SOURCE
:copyMainKotlinClasses
:processResources
:classes
:run
2017-09-05 22:40:53.701 [main] DEBUG ktor.application - Java Home: /Library/Java/JavaVirtualMachines/jdk1.8.0_40.jdk/Contents/Home
2017-09-05 22:40:53.712 [main] DEBUG ktor.application - Class Loader: sun.misc.Launcher$AppClassLoader@73d16e93: [/Users/jack/easykotlin/ktor_demo/build/classes/java/main/, /Users/jack/easykotlin/ktor_demo/build/resources/main/, /Users/jack/.gradle/caches/modules-2/files-2.1/org.jetbrains.ktor/ktor-netty/0.4.0/f8809d15d9b447b669e8514f14addcb3586dcd26/ktor-netty-0.4.0.jar, /Users/jack/.gradle/caches/modules-2/files-2.1/org.jetbrains.ktor/ktor-hosts-common/0.4.0/bec3be6cc48a989347a7d3048266aff412d16668/ktor-hosts-common-0.4.0.jar, /Users/jack/.gradle/caches/modules-2/files-2.1/org.jetbrains.ktor/ktor-core/0.4.0/be0937d74f19862e8087b08a3b2306de65aa6f12/ktor-core-0.4.0.jar, ...
2017-09-05 22:40:53.717 [main] INFO  ktor.application - No ktor.deployment.watch patterns match classpath entries, automatic reload is not active
2017-09-05 22:40:54.364 [main] TRACE ktor.application - Application started: org.jetbrains.ktor.application.Application@f78a47e

```


浏览器访问： http://127.0.0.1:8080/

![螢幕快照 2017-09-05 22.43.19.png](http://upload-images.jianshu.io/upload_images/1233356-5618de0401aa8572.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![螢幕快照 2017-09-05 22.43.26.png](http://upload-images.jianshu.io/upload_images/1233356-c109b28a6fea01cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![螢幕快照 2017-09-05 22.43.34.png](http://upload-images.jianshu.io/upload_images/1233356-ba4a3c93a8160614.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




# 完整工程示例代码

https://github.com/EasyKotlin/ktor_demo




# 参考资料：

 [http://ktor.io](http://ktor.io/)
 
 
 
 ----------------------------
# 《Kotlin极简教程》 正式上架：

Official on shelves: Kotlin Programming minimalist tutorial

> 京东JD：https://item.jd.com/12181725.html

> 天猫Tmall：https://detail.tmall.com/item.htm?id=558540170670
