### 4.1.Spring 的IOC和AOP机制？

我们是在使用Spring 框架的过程中，其实就是为了使用IOC，依赖注入，和AOP，面向切面编程，这两个是Spring 的灵魂

主要用到的设计模式有工厂模式和代理模式。

IOC就是典型的工厂模式，通过sessionfactory去注入实例。

AOP就是典型的代理模式的体现。

代理模式是常用的java设计模式，他的特征是代理类与委托类有同样的接口，代理类主要负责为委托类预处理消息、过滤消息、把消息转发给委托类，以及事后处理消息等。代理类与委托类之间通常会存在关联关系，一个代理类的对象与一个委托类的对象关联，代理类的对象本身并不真正实现服务，而是通过调用委托类的对象的相关方法，来提供特定的服务。

Spring 的IOC容器是Spring 的核心，Spring  AOP是Spring 框架的重要组成部分。在传统的程序设计中，当调用者需要被调用者的协助时，通常由调用者来创建被调用者的实例。但在Spring 里创建被调用者的工作不再由调用者来完成，因此控制反转（IOC）；创建被调用者实例的工作通常由Spring 容器来完成，然后注入调用者，因此也被称为依赖注入（DI），依赖注入和控制反转是同一个概念。

面向切面编程（AOP)是以另一个角度来考虑程序结构，通过分析程序结构的关注点来完善面向对象编程（OOP）。OOP将应用程序分解成各个层次的对象，而AOP将程序分解成多个切面。Spring  AOP 只实现了方法级别的连接点，在J2EE应用中，AOP拦截到方法级别的操作就已经足够。在Spring 中，未来使IOC方便地使用健壮、灵活的企业服务，需要利用Spring  AOP实现为IOC和企业服务之间建立联系。

**IOC:控制反转也叫依赖注入。利用了工厂模式将对象交给容器管理，你只需要在Spring 配置文件中配置相应的bean，以及设置相关的属性，让Spring 容器来生成类的实例对象以及管理对象。在Spring 容器启动的时候，Spring 会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类（假设这个类名是A），分配的方法就是调用A的setter方法来注入，而不需要你在A里面new这些bean了。**

Spring  IOC初始化流程

![在这里插入图片描述](https://img-blog.csdnimg.cn/426df75de548403a89426cf3d28e65b0.png)

**AOP:面向切面编程。（Aspect-Oriented Programming）**<br>
**AOP可以说是对OOP的补充和完善。OOP引入封装、继承和多态性等概念来建立一种对象层次结构，用以模拟公共行为的一个集合。当我们需要为分散的对象引入公共行为的时候，OOP则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。**

**将程序中的交叉业务逻辑（比如安全，日志，事务等），封装成一个切面，然后注入到目标对象（具体业务逻辑）中去。**

**实现AOP的技术，主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的方式，引入特定的语法创建“切面”，从而使得编译器可以在编译期间织入有关“切面”的代码.**

简单点解释，比方说你想在你的biz层所有类中都加上一个打印‘你好’的功能,这时就可以用aop思想来做.你先写个类写个类方法，方法经实现打印‘你好’,然后IOC这个类 ref＝“biz.*”让每个类都注入即可实现。

### 4.2.Spring 中Autowired和Resource关键字的区别？

@Resource和@Autowired都是做bean的注入时使用，其实@Resource并不是Spring 的注解，它的包是javax.annotation.Resource，需要导入，但是Spring 支持该注解的注入。

> 1. 共同点
>
>    两者都可以写在字段和setter方法上。两者如果都写在字段上，那么就不需要再写setter方法。
>
> 2. 不同点
>
>    （1）@Autowired
>
>    @Autowired为Spring 提供的注解，需要导入包
>
>    org.Spring framework.beans.factory.annotation.Autowired;只按照byType注入。

```java
public class TestServiceImpl {
  // 下面两种@Autowired只要使用一种即可
  @Autowired
  private UserDao userDao; // 用于字段上

  @Autowired
  public void setUserDao(UserDao userDao) { // 用于属性的方法上
    this.userDao = userDao;
 }
}
```

> @Autowired注解是按照类型（byType）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它的required属性为false。如果我们想使用按照名称（byName）来装配，可以结合@Qualifier注解一起使用。如下：

```java
public class TestServiceImpl {
  @Autowired
  @Qualifier("userDao")
  private UserDao userDao;
}
```

> （2）@Resource
>
> @Resource默认按照ByName自动注入，由J2EE提供，需要导入包javax.annotation.Resource。
>
> @Resource有两个重要的属性：name和type，而Spring 将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以，如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不制定name也不制定type属性，这时将通过反射机制使用byName自动注入策略。

```java
public class TestServiceImpl {
  // 下面两种@Resource只要使用一种即可
  @Resource(name="userDao")
  private UserDao userDao; // 用于字段上

  @Resource(name="userDao")
  public void setUserDao(UserDao userDao) { // 用于属性的setter方法上
    this.userDao = userDao;
 }
}
```

注：最好是将@Resource放在setter方法上，因为这样更符合面向对象的思想，通过set、get去操作属性，而不是直接去操作属性。

**@Resource装配顺序：**

> ①如果同时指定了name和type，则从Spring 上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。
>
> ②如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。
>
> ③如果指定了type，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。
>
> ④如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。
>
> @Resource的作用相当于@Autowired，只不过@Autowired按照byType自动注入

### 4.3.依赖注入的方式有几种，各是什么?

> 一、构造器注入
>
> 将被依赖对象通过构造函数的参数注入给依赖对象，并且在初始化对象的时候注入。
>
> 优点：对象初始化完成后便可获得可使用的对象。
>
> 缺点：当需要注入的对象很多时，构造器参数列表将会很长；不够灵活。若有多种注入方式，每种方式只需注入指定几个依赖，那么就需要提供多个重载的构造函数，麻烦。
>
> 二、setter方法注入
>
> IOC Service Provider通过调用成员变量提供的setter函数将被依赖对象注入给依赖类。
>
> 优点：灵活。可以选择性地注入需要的对象。
>
> 缺点：依赖对象初始化完成后由于尚未注入被依赖对象，因此还不能使用。
>
> 三、接口注入
>
> 依赖类必须要实现指定的接口，然后实现该接口中的一个函数，该函数就是用于依赖注入。该函数的参数就是要注入的对象。
>
> 优点：接口注入中，接口的名字、函数的名字都不重要，只要保证函数的参数是要注入的对象类型即可。
>
> 缺点：侵入性太强，不建议使用。
>
> PS：什么是侵入性？
>
> 如果类A要使用别人提供的一个功能，若为了使用这功能，需要在自己的类中增加额外的代码，这就是侵入性。

### 4.4.讲一下什么是Spring 

Spring 是一个轻量级的IOC和AOP容器框架。是为Java应用程序提供基础性服务的一套框架，目的是用于简化企业应用程序的开发，它使得开发者只需要关心业务需求。常见的配置方式有三种：基于XML的配置、基于注解的配置、基于Java的配置。

主要由以下几个模块组成：

> Spring  Core：核心类库，提供IOC服务；
>
> Spring  Context：提供框架式的Bean访问方式，以及企业级功能（JNDI、定时任务等）；
>
> Spring  AOP：AOP服务；
>
> Spring  DAO：对JDBC的抽象，简化了数据访问异常的处理；
>
> Spring  ORM：对现有的ORM框架的支持；
>
> Spring  Web：提供了基本的面向Web的综合特性，例如多方文件上传；
>
> Spring  MVC：提供面向Web应用的Model-View-Controller实现。

### 4.5.Spring  MVC流程

工作原理：

![在这里插入图片描述](https://img-blog.csdnimg.cn/ccb4cb468cb64488a261f2d0ecf369f5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)

> 1、 用户发送请求至前端控制器DispatcherServlet。
>
> 2、 DispatcherServlet收到请求调用HandlerMapping处理器映射器。
>
> 3、 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
>
> 4、 DispatcherServlet调用HandlerAdapter处理器适配器。
>
> 5、 HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。
>
> 6、 Controller执行完成返回ModelAndView。
>
> 7、 HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
>
> 8、 DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
>
> 9、 ViewReslover解析后返回具体View。
>
> 10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）
>
> 11、 DispatcherServlet响应用户

**组件说明：**
以下组件通常使用框架提供实现：

> DispatcherServlet：作为前端控制器，整个流程控制的中心，控制其它组件执行，统一调度，降低组件之间的耦合性，提高每个组件的扩展性。
>
> HandlerMapping：通过扩展处理器映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。
>
> HandlAdapter：通过扩展处理器适配器，支持更多类型的处理器。
>
> ViewResolver：通过扩展视图解析器，支持更多类型的视图解析，例如：jsp、freemarker、pdf、excel等。

**在讲Spring MVC之前我们先来看一下什么是MVC模式**
MVC：MVC是一种设计模式

MVC的原理图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/6ebec402054549c8b43c2290737e15c2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA55m95aSn6ZSF,size_20,color_FFFFFF,t_70,g_se,x_16)


分析：

> M-Model 模型（完成业务逻辑：有javaBean构成，service+dao+entity）
>
> V-View 视图（做界面的展示 jsp，html……）
>
> C-Controller 控制器（接收请求—>调用模型—>根据结果派发页面）

Spring MVC是什么：

> Spring MVC是一个MVC的开源框架，Spring MVC=struts2+Spring ，Spring MVC就相当于是Struts2加上sring的整合，但是这里有一个疑惑就是，Spring MVC和Spring 是什么样的关系呢？这个在百度百科上有一个很好的解释：意思是说，Spring MVC是Spring 的一个后续产品，其实就是Spring 在原有基础上，又提供了web应用的MVC模块，可以简单的把Spring MVC理解为是Spring 的一个模块（类似AOP，IOC这样的模块），网络上经常会说Spring MVC和Spring 无缝集成，其实Spring MVC就是Spring 的一个子模块，所以根本不需要同Spring 进行整合。

### 4.6.Spring MVC怎么样设定重定向和转发的？

（1）转发：在返回值前面加"forward:"，譬如"forward:user.do?name=method4"

（2）重定向：在返回值前面加"redirect:"，如"redirect:http://www.baidu.com"

### 4.7.Spring MVC常用的注解有哪些？

> @RequestMapping：用于处理请求 url 映射的注解，可用于类或方法上。用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径。
>
> @RequestBody：注解实现接收http请求的json数据，将json转换为java对象。
>
> @ResponseBody：注解实现将conreoller方法返回对象转化为json对象响应给客户

### 4.8.Spring 的AOP理解：

OOP面向对象，允许开发者定义纵向的关系，但并适用于定义横向的关系，导致了大量代码的重复，而不利于各个模块的重用。

AOP，一般称为面向切面，作为面向对象的一种补充，用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块，这个模块被命名为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。可用于权限认证、日志、事务处理。

AOP实现的关键在于 代理模式，AOP代理主要分为静态代理和动态代理。静态代理的代表为AspectJ；动态代理则以Spring  AOP为代表。

> （1）AspectJ是静态代理的增强，所谓静态代理，就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，他会在编译阶段将AspectJ(切面)织入到Java字节码中，运行的时候就是增强之后的AOP对象。
>
> （2）Spring  AOP使用的动态代理，所谓的动态代理就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。
>
> （3）静态代理与动态代理区别在于生成AOP代理对象的时机不同，相对来说AspectJ的静态代理方式具有更好的性能，但是AspectJ需要特定的编译器进行处理，而Spring  AOP则无需特定的编译器处理

Spring  AOP中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理：

> ①JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。
>
> ②如果代理类没有实现 InvocationHandler 接口，那么Spring  AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的

### 4.9.Spring 的IOC理解

> （1）IOC就是控制反转，是指创建对象的控制权的转移，以前创建对象的主动权和时机是由自己把控的，而现在这种权力转移到Spring 容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用。DI依赖注入，和控制反转是同一个概念的不同角度的描述，即 应用程序在运行时依赖IOC容器来动态注入对象需要的外部资源。
>
> （2）最直观的表达就是，IOC让对象的创建不用去new了，可以由Spring 自动生产，使用java的反射机制，根据配置文件在运行时动态的去创建对象以及管理对象，并调用对象的方法的
>
> （3）Spring 的IOC有三种注入方式 ：构造器注入、setter方法注入、根据注解注入。
>
> IOC让相互协作的组件保持松散的耦合，而AOP编程允许你把遍布于应用各层的功能分离出来形成可重用的功能组件。

### 4.10.解释一下Spring  bean的生命周期

> 1. 实例化Bean
>
> 2. Spring 注入Bean的属性
>
> 3. 检查`Aware`相关接口并设置相关依赖
>
>    ------
>
> 4. `BeanPostProcessor`前置处理，调用`postProcessBeforeInitialization`方法
>
> 5. 检查是否是`InitializingBean`以决定是否调用`afterPropertiesSet`方法
>
> 6. 检查是否配置有自定义的`init-method`方法
>
> 7. `BeanPostProcessor`后置处理，调用`postProcessAfterInitialization`方法
>
>    ------
>
> 8. Bean准备就绪可以被使用
>
> 9. 是否实现`DisposableBean`接口，销毁时调用`destory`
>
> 10. 检查是否配置有自定义的`destory-method`方法

![](https://images2015.cnblogs.com/blog/618347/201606/618347-20160627154632859-212137896.png)

### 4.11.解释Spring 支持的几种bean的作用域。

Spring 容器中的bean可以分为5个范围：

> （1）singleton：默认，每个容器中只有一个bean的实例，单例的模式由BeanFactory自身来维护。
>
> （2）prototype：为每一个bean请求提供一个实例。
>
> （3）request：为每一个网络请求创建一个实例，在请求完成以后，bean会失效并被垃圾回收器回收。
>
> （4）session：与request范围类似，确保每个session中有一个bean的实例，在session过期后，bean会随之失效。
>
> （5）global-session：全局作用域，global-session和Portlet应用相关。当你的应用部署在Portlet容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存储在global-session中。全局作用域与Servlet中的session作用域效果相同。

### 4.12. Spring 基于xml注入bean的几种方式：

> （1）Set方法注入；
>
> （2）构造器注入：①通过index设置参数的位置；②通过type设置参数类型；
>
> （3）静态工厂注入；
>
> （4）实例工厂；

### 4.13.Spring 框架中都用到了哪些设计模式？

> （1）工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例；
>
> （2）单例模式：Bean默认为单例模式。
>
> （3）代理模式：Spring 的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术；
>
> （4）模板方法：用来解决代码重复的问题。比如RestTemplate,JmsTemplate, JpaTemplate。
>
> （5）观察者模式：定义对象键一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都会得到通知被制动更新，如Spring 中listener的实现–ApplicationListener

### 4.14 Spring 是怎么解决的循环依赖？为啥Spring 不能解决“A的构造方法中依赖了B的实例对象

> `三级缓存`
>
> 三级 啥都还没有干,给了工厂 singletonFactories ： 单例对象工厂的cache
>
> 二级,完成了构造函数但是还没有注意依赖earlySingletonObjects ：提前暴光的单例对象的Cache
>
> 一级 加载好的beansingletonObjects：单例对象的cache

### 4.15 Spring 中的事务传播机制？事务嵌套

`定义：`Spring 事务传播机制就是**多个事务方法相互调用时,事务如何在这些方法间传播**。

> 举个栗子，方法A是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。

**七个传播机制**

> 1.`REQUIRED`(Spring 默认的事务传播类型):
> 如果当前没有事务，则自己新建一个事务，如果当前存在事务，则加入这个事务。
>
> **举例说明:**
>
> 如果方法A所在的方法里面的sql没有事务，那么就会与方法B里面的sql事务放在一起，要么同时成功，要么同时失败。如果方法A所在的方法里面的sql有事务，那么方法B所在的方法里面的sql就会加入方法A的sql的事务，要么同时成功，要么同时失败。
>
> 2.`SUPPORTS`:
> 如果当前存在事务，则加入当前事务，如果当前不存在事务，则以非事务方法执行。
>
> **举例说明:**
>
> 如果方法A所在的方法里面的sql有事务，那么方法B里面的sql则会加入方法A的事务，要么同时成功，要么同时失败。如果方法A所在的方法里面的sql没有事务，那么方法B所在的方法里面的sql就会以非事务运行。
>
> 3.`MANDATORY`:
> 如果当前存在事务，则加入当前事务，如果当前不存在事务，则抛出异常。
>
> **举例说明:**
>
> 如果方法A所在的方法里面的sql有事务，那么方法B里面的sql则会加入方法A的事务，要么同时成功，要么同时失败。如果方法A所在的方法里面的sql没有事务，那么方法B所在的方法就会抛出异常。
>
> 4.`REQUIRES_NEW`：
> 创建一个新事务，如果当前存在事务，则挂起该事务。
>
> **举例说明:**
>
> 如果方法A所在的方法里面的sql有事务，同时方法B所在的方法里面的sql也有事务，那么先执行方法A里面的事务，再去执行方法B里面的事务。这种情况下，A事务回滚就只是回滚A自己的事务，B亦是如此。
>
> 5.`NOT_SUPPORTED`：
> 以非事务方式执行，如果当前存在事务，则挂起该事务。
>
> **举例说明:**
>
> 如果方法A所在的方法里面的sql有事务，那么方法A里面的sql单独在事务里执行，方法B里面的sql一定是以非事务运行。如果方法A里面的sql没有事务，那么方法A与方法B里面的sql都是以非事务方式执行。
>
> 6.`NEVER`：
> 不使用事务，如果当前存在事务，则抛出异常。
>
> 7.`NESTED`：
> 如果当前存在事务，则在嵌套事务内执行，如果当前不存在事务，则新建一个事务。
>
> **举例说明:**
>
> 如果方法A所在的sql有事务，那么方法B所在的sql则会嵌套在方法A的事务中执行。

**NESTED和REQUIRES_NEW的区别:**

> REQUIRES_NEW是新建一个事务并且新开启的这个事务与原事务无关，而NESTED则是当前存在事务时（我们把当前事务称之为父事务）会开启一个嵌套事务（称之为一个子事务）。在NESTED情况下父事务回滚时，子事务也会回滚，而在REQUIRES_NEW的情况下，原有事务回滚，不会影响新开启的事务。

### 4.16 Spring 中同一个类中有方法A 和 B 两个方法都被标记了@Transtional，在A中调用了B，那么B的事务会生效吗？为什么？

> https://blog.csdn.net/qq_39597203/article/details/87434879

- 不同类之间的方法调用，如类A的方法a()调用类B的方法b()，这种情况事务是正常起作用的。只要方法a()或b()配置了事务，运行中就会开启事务，产生代理。
- 同一类内方法调用，无论被调用的b()方法是否配置了事务，此事务在被调用时都将不生效。

**原因：**

>   当从类外调用方法a()时，从Spring 容器获取到的serviceImpl对象实际是包装好的proxy对象，因此调用a()方法的对象是动态代理对象。
>
> 而在类内部a()调用b()的过程中，实质执行的代码是this.b()，此处this对象是实际的serviceImpl对象而不是本该生成的代理对象，因此直接调用了b()方法。

**解决办法:**

> 1. 放到不同的类中进行调用
>
> 2. 在Spring 配置文件中加入配置
>
>    ```xml
>    <aop:aspectj-autoproxy/>
>    <aop:aspectj-autoproxy proxy-target-class=“true” expose-proxy=“true” />
>    ```
>
> 3. 将之前使用普通调用的方法,换成使用代理调用
>
>    ```java
>    ((TestService)AopContext.currentProxy()).testTransactional2();
>    ```
>
>    获取到TestService的代理类，再调用事务方法，强行经过代理类，激活事务切面。
>
> 4. 使用异步操作，另外开启一个线程或者将这个消息写入到队列里面，在其他的地方进行处理

### 4.17.**Spring 事务的种类**

Spring 支持编程式事务管理和声明式事务管理两种方式：

①编程式事务管理使用TransactionTemplate。

②声明式事务管理建立在AOP之上的。其本质是通过AOP功能，对方法前后进行拦截，将事务处理的功能编织到拦截的方法中，也就是在目标方法开始之前启动一个事务，在执行完目标方法之后根据执行情况提交或者回滚事务。

> 声明式事务最大的优点就是不需要在业务逻辑代码中掺杂事务管理的代码，只需在配置文件中做相关的事务规则声明或通过@Transactional注解的方式，便可以将事务规则应用到业务逻辑中，减少业务代码的污染。唯一不足地方是，最细粒度只能作用到方法级别，无法做到像编程式事务那样可以作用到代码块级别。
