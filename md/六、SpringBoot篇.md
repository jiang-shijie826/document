### 6.1.什么是SpringBoot？为什么要用SpringBoot

用来简化spring应用的初始搭建以及开发过程 使用特定的方式来进行配置（properties或yml文件）

创建独立的spring应用程序 main方法运行

嵌入的Tomcat 无需部署war文件简化maven配置

自动配置spring添加对应功能starter自动化配置

**spring boot来简化spring应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用**

Spring Boot 优点非常多，如：

> 一、独立运行
>
> Spring Boot而且内嵌了各种servlet容器，Tomcat、Jetty等，现在不再需要打成war包部署到容器中，Spring Boot只要打成一个可执行的jar包就能独立运行，所有的依赖包都在一个jar包内。
>
> 二、简化配置
>
> spring-boot-starter-web启动器自动依赖其他组件，简少了maven的配置。
>
> 三、自动配置
>
> Spring Boot能根据当前类路径下的类、jar包来自动配置bean，如添加一个spring-boot-starter-web启动器就能拥有web的功能，无需其他配置。
>
> 四、无代码生成和XML配置
>
> Spring Boot配置过程中无代码生成，也无需XML配置文件就能完成所有配置工作，这一切都是借助于条件注解完成的，这也是Spring4.x的核心功能之一。
>
> 五、应用监控
>
> Spring Boot提供一系列端点可以监控服务及应用，做健康检测。

### 6.2.Spring Boot 的核心注解是哪个？它主要由哪几个注解组成的？

启动类上面的注解是@SpringBootApplication，它也是 Spring Boot 的核心注解，主要组合包含了以下
3 个注解：

**@SpringBootConfiguration：**组合了 @Configuration 注解，实现配置文件的功能。

**@EnableAutoConfiguration：**打开自动配置的功能，也可以关闭某个自动配置的选项，如关闭数据源

**自动配置功能：** 

@SpringBootApplication(exclude = { DataSourceAutoConfiguration.class })。

@ComponentScan：Spring组件扫描。

### 6.3.运行Spring Boot有哪几种方式？

> 1）打包用命令或者放到容器中运行
>
> 2）用 Maven/Gradle 插件运行
>
> 3）直接执行 main 方法运行

### 6.4.如何理解 Spring Boot 中的 Starters？

Starters是什么：

> Starters可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包，你可以一站式集成Spring及其他技术，而不需要到处找示例代码和依赖包。如你想使用Spring JPA访问数据库，只要加入spring-boot-starter-data-jpa启动器依赖就能使用了。Starters包含了许多项目中需要用到的依赖，它们能快速持续的运行，都是一系列得到支持的管理传递性依赖。
>
> Starters命名：
>
> Spring Boot官方的启动器都是以spring-boot-starter-命名的，代表了一个特定的应用类型。第三方的启动器不能以spring-boot开头命名，它们都被Spring Boot官方保留。一般一个第三方的应该这样命名，像mybatis的mybatis-spring-boot-starter。

Starters分类：

1. Spring Boot应用类启动器

![在这里插入图片描述](https://img-blog.csdnimg.cn/423280fdbb4d4228886f1b7f24d1f3de.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)

2. Spring Boot生产启动器

![在这里插入图片描述](https://img-blog.csdnimg.cn/f8987e9903934e928be60ef79c83556a.png)

3. Spring Boot技术类启动器

![在这里插入图片描述](https://img-blog.csdnimg.cn/6160b2f4019a4782ac053c8143c7fefc.png)

4. 其他第三方启动器

### 6.5.如何在Spring Boot启动的时候运行一些特定的代码？

如果你想在Spring Boot启动的时候运行一些特定的代码，你可以实现接口ApplicationRunner或者CommandLineRunner，这两个接口实现方式一样，它们都只提供了一个run方法。

**CommandLineRunner：**启动获取命令行参数

### 6.6.Spring Boot 需要独立的容器运行吗？

可以不需要，内置了 Tomcat/ Jetty 等容器。

### 6.7. Spring Boot中的监视器是什么？

> Spring boot actuator是spring启动框架中的重要功能之一。Spring boot监视器可帮助您访问生产环境中正在运行的应用程序的当前状态。有几个指标必须在生产环境中进行检查和监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警报消息。监视器模块公开了一组可直接作为HTTP URL访问的REST端点来检查状态

### 6.8. 如何使用Spring Boot实现异常处理？

Spring提供了一种使用ControllerAdvice处理异常的非常有用的方法。 我们通过实现一个ControllerAdvice类，来处理控制器类抛出的所有异常

### 6.9.你如何理解 Spring Boot 中的 Starters？

Starters可以理解为启动器，它包含了一系列可以集成到应用里面的依赖包，你可以一站式集成 Spring及其他技术，而不需要到处找示例代码和依赖包。如你想使用 Spring JPA 访问数据库，只要加入spring-boot-starter-data-jpa 启动器依赖就能使用了

### 6.10. springboot常用的starter有哪些

> spring-boot-starter-web 嵌入tomcat和web开发需要servlet与jsp支持
>
> spring-boot-starter-data-jpa 数据库支持
>
> spring-boot-starter-data-redis redis数据库支持
>
> spring-boot-starter-data-solr solr支持
>
> mybatis-spring-boot-starter 第三方的mybatis集成starter

### 6.11.SpringBoot 实现热部署有哪几种方式？

主要有两种方式：

> Spring Loaded
>
> Spring-boot-devtools

### 6.12.如何理解 Spring Boot 配置加载顺序？

在 Spring Boot 里面，可以使用以下几种方式来加载配置。

> 1）properties文件；
>
> 2）YAML文件；
>
> 3）系统环境变量；
>
> 4）命令行参数；

### 6.13.Spring Boot 的核心配置文件有哪几个？它们的区别是什么？

spring Boot 的核心配置文件是 application 和 bootstrap 配置文件。

application 配置文件这个容易理解，主要用于 Spring Boot 项目的自动化配置。

bootstrap 配置文件有以下几个应用场景：

> 1.使用 Spring Cloud Config 配置中心时，这时需要在 bootstrap 配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
>
> 2.一些固定的不能被覆盖的属性；
>
> 3.一些加密/解密的场景；

### 6.14.如何集成 Spring Boot 和 ActiveMQ？

对于集成 Spring Boot 和 ActiveMQ，我们使用spring-boot-starter-activemq依赖关系。 它只需要很少的配置，并且不需要样板代码。

### 6.15.如何重新加载Spring Boot上的更改，而无需重新启动服务器？

这可以使用DEV工具来实现。通过这种依赖关系，您可以节省任何更改，嵌入式tomcat将重新启动。

Spring Boot有一个开发工具（DevTools）模块，它有助于提高开发人员的生产力。Java开发人员面临的一个主要挑战是将文件更改自动部署到服务器并自动重启服务器。

开发人员可以重新加载Spring Boot上的更改，而无需重新启动服务器。这将消除每次手动部署更改的需要。Spring Boot在发布它的第一个版本时没有这个功能。

这是开发人员最需要的功能。DevTools模块完全满足开发人员的需求。该模块将在生产环境中被禁用。

它还提供H2数据库控制台以更好地测试应用程序。

org.springframework.boot

spring-boot-devtools

true

### 6.16.Spring Boot、Spring MVC 和 Spring 有什么区别？

> 1、Spring
>
> Spring最重要的特征是依赖注入。所有 SpringModules 不是依赖注入就是 IOC 控制反转。
>
> 当我们恰当的使用 DI 或者是 IOC 的时候，我们可以开发松耦合应用。松耦合应用的单元测试可以很容易的进行。
>
> 2、Spring MVC
>
> Spring MVC 提供了一种分离式的方法来开发 Web 应用。通过运用像 DispatcherServelet，MouduleAndView 和 ViewResolver 等一些简单的概念，开发 Web 应用将会变的非常简单。
>
> 3、SpringBoot
>
> Spring 和 SpringMVC 的问题在于需要配置大量的参数。

![在这里插入图片描述](https://img-blog.csdnimg.cn/604bc3608ed540a4b462b1cd42bdcd36.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)


Spring Boot 通过一个自动配置和启动的项来目解决这个问题。为了更快的构建产品就绪应用程序，Spring Boot 提供了一些非功能性特征

### 6.17.能否举一个例子来解释更多 Staters 的内容？

让我们来思考一个 Stater 的例子 -Spring Boot Stater Web。

如果你想开发一个 web 应用程序或者是公开 REST 服务的应用程序。Spring Boot Start Web 是首选。

让我们使用 Spring Initializr 创建一个 Spring Boot Start Web 的快速项目。

Spring Boot Start Web 的依赖项

![在这里插入图片描述](https://img-blog.csdnimg.cn/d725bde8e7ee4cf8b4a938413572887e.png)

下面的截图是添加进我们应用程序的不同的依赖项

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbfba60c4ed1481998d9eb234884b242.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)

依赖项可以被分为：

> Spring - core，beans，context，aop
>
> Web MVC - （Spring MVC）
>
> Jackson - for JSON Binding
>
> Validation - Hibernate,Validation API
>
> Enbedded Servlet Container - Tomcat
>
> Logging - logback,slf4j

任何经典的 Web 应用程序都会使用所有这些依赖项。Spring Boot Starter Web 预先打包了这些依赖项。作为一个开发者，我不需要再担心这些依赖项和它们的兼容版本

### 6.18.Spring Boot 还提供了其它的哪些 Starter Project Options？

Spring Boot 也提供了其它的启动器项目包括，包括用于开发特定类型应用程序的典型依赖项。

> spring-boot-starter-web-services - SOAP Web Services；
>
> spring-boot-starter-web - Web 和 RESTful 应用程序；
>
> spring-boot-starter-test - 单元测试和集成测试；
>
> spring-boot-starter-jdbc - 传统的 JDBC；
>
> spring-boot-starter-hateoas - 为服务添加 HATEOAS 功能；
>
> spring-boot-starter-security - 使用 SpringSecurity 进行身份验证和授权；
>
> spring-boot-starter-data-jpa - 带有 Hibeernate 的 Spring Data JPA；
>
> spring-boot-starter-data-rest - 使用 Spring Data REST 公布简单的 REST 服务；

