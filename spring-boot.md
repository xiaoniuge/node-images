# Spring Boot 2.2.1

##### 一、（Introducing Spring Boot）简介

​	Spring Boot makes it easy to create stand-alone , production-grade Spring-base Application that you can run. (Spring Boot 让我们可以轻松的创建一个基于Spring-base，独立的、生产级别的应用)

​	We take an opinionated view of the Spring platform and thirty-party libraries , so that you can get started with minimum fuss . (我们对spring平台和三方依赖使用一种独特的表现方式，所以我们几乎可以毫无顾虑的开始使用他)

​	Most Spring Boot applications need very little Spring configuration.(绝大多数的Spring Boot 应用 只需要极少的Spring 配置)

##### 二、（feature）特点

- `Provide a  radically faster and widely accessiable getting-started experience for all Spring development.`(**为所有的Spring开发提供一个快捷创建、且容易学习的框架**)
-  `Be opinionated out of the box but get out of the way quickly as requirements start to diverge from the defaults.` （**开箱即用，从配置中摆脱出来，快速开始业务**）
-  `Provide a range of non-functional features that are common to large classes of projects (such as embedded servers, security, metrics, health checks, and externalized configuration).` （**提供一系列非功能性特性，这些特性是大型项目类所共有的（如：嵌入式服务器、安全性、度量、运行状况检查和外部化配置）**）
-  `Absolutely no code generation and no requirement for XML configuration.` （**完全没有代码生成，也不需要XML配置。**）

##### 三、（System Requriements）系统要求

- Java 8 to Java 13
- Spring Framework 5.2.1.RELEASE or above is also required. 
- build tools maven and gradle 

##### 四、（Servlet Containers）容器

| Name         | Servlet Version |
| ------------ | --------------- |
| Tomcat 9.0   | 4.0             |
| Jetty 9.4    | 3.1             |
| Undertow 2.0 | 4.0             |

##### 五、starters



### 2.3。应用程序属性文件

`SpringApplication`从`application.properties`以下位置的文件加载属性并将其添加到Spring中`Environment`：

1. 一个`/config`当前目录的子目录
2. 当前目录
3. 类路径`/config`包
4. 类路径根

该列表按优先级排序（在列表较高位置定义的属性会覆盖在较低位置定义的属性）。

