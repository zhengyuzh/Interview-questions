

# 41、spring的事务传播机制
多个事务方法相互调用时，事务如何在这些方法间传播

方法A是一个事务的方法，方法A执行过程中调用了方法B，那么方法B有无事务以及方法B对事务的要求不同都会对方法A的事务具体执行造成影响，同时方法A的事务对方法B的事务执行也有影响，这种影响具体是什么就由两个方法所定义的事务传播类型所决定。

- `REQUIRED`（spring默认的事务传播类型）：如果当前没有事务，则自己新建一个事务，如果当前存在事务，==则加入这个事务==

- `SUPPORTS`：当前存在事务，则加入当前事务，如果当前没有事务，就==以非事务方法执行==

- `MANDATORY`：当前存在事务，则加入当前事务，如果当前事务不存在，==则抛出异常==

- `REQUIRES_NEW`：创建一个新事务，如果存在当前事务，则挂起该事务

- `NOT_SUPPORTED`：以非事务方式执行，如果当前存在事务，则挂起当前事务

- `NEVER`:不适用事务，如果当前事务存在，则抛出异常

- `NESTED`：如果当前事务存在，则在嵌套事务中执行，否则REQUIRED的操作一样（开启一个事务）

和`REQUIRES_NEW`的区别：

- `REQUIRES_NEW`是新建一个事务并且新开启的这个事务与==原有事务无关==，而`NESTED`则是当前存在事务时（我们把当前事务称之为父事务）会开启一个嵌套事务（称之为一个子事务）。在`NESTED`情况下父事务回滚时，子事务也会回滚，而在`REQUIRES_NEW`情况下，原有事务回滚，不会影响新开启的事务

和REQUIRED的区别
- REQUIRED情况下，调用方法存在事务时，则被调用方和调用方使用同一事务，那么被调用方法出现异常时，由于共用一个事务，所有无论调用方是否catch其异常，事务都会回滚 。而在NESTED情况下，被调用方发生异常时，调用方可以catch其异常，这样只有子事务回滚，父事务不受影响。

# 42 、spring事务什么时候会失效
spring事务的原理AOP，进行了切面增强，那么失效的根本原因是这个AOP不起作用了！常见情况有如下几种
- 1 、 发生自调用，类里面使用this调用本类的方法（this通常省略），此时这个this对象不是代理类，而是UserService对象本身！
==解决办法很简单：让那个this变成UserService的代理类即可。==

- 2、方法不是public的
@Transactional 只能用于public 的方法上，否则事务不会失效，如果要用在非public方法上，可以开启Aspectj代理模式。

- 3 、数据库不支持事务
- 4 、没有被sprin管理
- 5 、异常被吃掉，事务不会回滚（或者抛出的异常没有被定义，默认为RuntimeException)


# 43 、什么的是bean的自动装配、有哪些方式?
开启自动装配，只需要在xml配置文件`<bean>`中定义“autowire"属性。

```
<bean id="cutomer" class = "com.xxx.xxx.Customer" autowire=" "  />
```
autowire属性有五种装配的方式：
- 1 、 no -缺省情况下，自动配置是通过”ref"属性手动设定。

```
手动装配：以value或ref的方式明确指定属性值都是手动装配
需要通过ref属性来连接bean
```
- 2 、byName-根据bean的属性名称进行自动装配。

```
Customer的属性名称是person，Spring会将bean id为person的bean通过setter方法进行自动装配。
<bean id = "customer" class = "com.xxx.xxx.Cutomer" autowire ="byName" />
<bean id ="person" class ="com.xxx.xxx.Person" />
```
- 3 、byType-根据bean的类型进行自动装配

```
Cutomer的属性person的类型为Person，Spring会将Person类型通过setter方法进行自动装配。
<bean id ="cutomer" class = "com.xxx.xxx.Cutomer" autowire ="byType" />
<bean id = "person" class ="com.xxx.xxx.Person" />
```
- 4 、 constructor - 类似byType，不过是应用于构造器的参数。如果一个bean与构造器参数的类型相同，则进行自动装配，否则导致异常。

```
Cutomer构造函数的参数person的类型为Person，Spring会将Person类型通过构造方法进行自动装配。
<bean id ="cutomer" class ="com.xxx.xxx.Cutomer" autowire ="construtor" />
<bean id ="person" class ="com.xxx.xxx.Person" />
```
- 5 、autodetect -如果有默认的构造器，则通过constructor方式进行自动装配，否则使用byType方式进行自动装配。

```
如果有默认的构造器，则通过constructor方式进行自动装配，否则使用byType方式进行自动装配。
```
@Autowired自动装配bean，可以在字段、setter方法，构造函数上使用。


# 44 、spring 、 springmvc 、springboot的区别
- ==spring==是一个IOC容器，用来管理Bean，使用依赖注入实现控制反转，可以很方便的整合各种框架，提供AOP机制弥补OOP的代码重复问题、更方便将不同类不同方法中的共同处理抽取成切面、自动注入给方法执行，比如日志、异常等

- ==springmvc==是spring对web框架的一个解决方案，提供了一个总的前端控制器servlet，用来接受请求，然后定义了一套路由策略（url到handle的映射）及适配执行handle，将handle结果使用视图解析器技术生成视图展现给前端

- ==springboot==是spring提供的一个快速开发工具包，让程序员能更方便、更快速的开发spring+springmvc应用，简化了配置（约定了默认配置），整合了一些列的解决方案（starter机制）、redis、mongodb、es、可以开箱即用。
# 45 、springmvc的工作流程
- 1 、用户发送请求至前端控制器DispatcherServlet.
- 2  、DispatcherServlet收到请求调用HandlerMapping处理器映射器
- 3 、处理器映射器找到具体的处理器（可以根据xml配置，注解进行查找），生成处理器及处理器拦截器（如果有则生成）一并返回给DispatcherServlet
- 4、DispatcherServlet调用HandlerAdapter处理器适配器
- 5、HandlerAdapter经过适配器调用具体的处理器（Controller，也叫后端控制器）
- 6、 Controller执行完成返回ModelAndView。
- 7、 HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
- 8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器
- 9、ViewReslover解析后返回具体View
- 10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）
- 11、DispatcherServlet响应用户

# 46 、Spring MVC的主要组件
Handler：也就是处理器。它直接应对着MVC中的C也就是Controller层，它的具体表现形式有很多，可以是==类==，可以是==方法==。在Controller层中`@RequestMapping`标注的所有方法都可以看成一个Handler，只要可以实际处理请求就可以是Handler。

- **1 、HandlerMapping**
initHandlerMappings（context），处理器映射器，根据用户请求的资源url来查找Handler的，在springMVC中会有很多请求，每个请求都需要一个Handler处理，具体接收到一个请求之后使用哪个Handler进行，这个就是HandlerMapping需要做的事。

- **2 、HandlerAdapter**
initHandlerAdapters（context），适配器。因为SpringMVC中的Handler可以是任意形式，只要能处理请求就ok，但是servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方法。如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢？这就是HandlerAdapter要做的事情。
Handler是用来干活的工具；HanderMapping用于根据需要干的活找到相应的工具；HandlerAdapter是使用工具干活的人。

- **3 、HandlerExceptionResolver**
initHandlerExceptionResolvers（context），其它组件都是用来干活的。在干活的过程中难免会出现问题，出问题后咋办？这就需要有一个专门的角色对异常情况进行处理，在SpringMVC中就是HandlerExceptionResolver。具体来说，此组件的作用是根据异常设置ModelAndView，之后再交给rende方法进行渲染。

- **4 、ViewResolver**
initViewResolver（context），ViewResolver用来将String类型的视图名和Local解析为View类型的视图。View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html（也可能是其他类型）文件。这里就有两个关键问题：使用哪个模板？用什么技术（规则）填入参数？这其实是ViewResolver主要做的工作，ViewResolver需要找到渲染所用的模板和所用的技术（也就是视图的类型）进行渲染，具体的渲染过程则交由不同的视图自己完成。

- **5 、RequestToViewNameTranslator**
initRequestToViewNameTranslator（context），ViewResolver是根据ViewName查找View，但有的Handler处理完后并没有设置ViewName，这时就需要从request获取ViewName了，如何从request中获取ViewName就是RequesToViewNameTranslator要做的事情。RequestToViewNameTranslator再SpringMVC容器里只可以配置一个，所以所有rqeust到ViewName的转换规则都要在一个Translator里面全部实现。

- **6 、LocalResolver**
initLocalResolver（context),解析视图需要两个参数：一是视图名，另一个是Locale。视图名是处理器返回的，Locale是从哪里来的？这就是LocaleResolver要做的事情。LocalResolver用于从reqeust解析出Locale，locale就是zh-cn之类，表示一个区域，有了这个就可以对不同的区域的用户显示不同的结果。SpringMVC主要有两个地方用到了Locale：一层是ViewResolver视图解析的时候，二是用到国际化资源或者主题的时候。

- **7 、ThemeResolver**
iniThemeResolver（context），用于解析主题。springMVC中一个主题对应一个properties文件，里面存放着跟当前主题相关的所有资源、如图片、css样式等。SpringMVC的主题也支持国际化，同一个主题不同区域也可以显示不同的风格。SpringMVC中跟主题相关的类有ThemeResolver、ThemeSource和Theme。主题是通过一系列资源来具体体现的，要得到要给主题的资源，首先要得到资源的名称，这是ThemeResover的工作。然后通过主题名称找到对应的主题（可以理解为一个配置）文件，这是ThemeSource的工作。最后从主题中获取资源就可以了

- **8 、MultipartResolver**
- initMultipartResolver（context），用于处理上传请求。处理方法是将普通的rrequest包装成MultipartHttpServletRequest，后者可以直接调用getFile方法获取File，如果上传多个文件，还可以调用getFileMap得到FileName->File结构的Map.此组件中一共有三个方法，作用分别是判断是不是上传请求，将reqeust包装成MultipartHttpServletRequest、处理完后清理上传过程中产生的临时资源
- 9 、FlashMapManager
initFlashMapManager（context），用来管理FlashMap的，FlashMap主要用在redirect中传递参数。


# 47 、springboot自动配置原理
@import+@Configuration+Spring spi
自动配置类由各个starter提供，使用@Configuration+@Bean定义配置类，放到META-INF/spring。factories下使用Spring spi 扫描META-INF/spring.factories下的配置类
使用@import导入自动装配类。

# 48 、 如何理解springboot的starter
使用spring+springmvc使用，如果需要引入mybatis等框架，需要到xml中定义mybatis需要的bean
starter就是定义要给starter的jar包，写一个@Configuration配置类、将这些bean定义在里面，然后再starter包的META-INF/spring.factories中写入该配置类，springboot会按照约定来加载该配置类。

开发人员只需要将相应的starter包依赖进应用，进行相应的属性配置（使用默认配置时，不许要配置），就可以直接进行代码开发，使用对应的功能了，比如mybatis-spring-boot-starter，spring-boot-starter-redis

# 49 、 什么是嵌入式服务器，为什么使用嵌入式服务器
节省了下载安装tomcat，应用也不再需要打war包，然后放到webapp目录下再运行，只需要一个安装了java的虚拟机，就可以直接在上面部署应用程序了。springboot已经内置了tomcat.jar。运行main方法时会去启动tomcat，并利用tomcat的spi机制加载springmvc

# 50、mybatis的优缺点
**优点：**
- 1 、基于sql语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，sql写在XML文件里，解除sql与程序代码的耦合，便于统一管理；提供了XML标签，支持编写动态sql语句，并可重用。
- 2 、与JDBC相比，减少了50%以上的代码量，消除了大量冗余的代码，不需要手动开关连接；
- 3 、很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）
- 4 、能够与Spring很好的集成
- 5 、提供映射标签，支持对象与数据库的ORM字段映射；提供对象关系映射标签，支持对象关系组件维护。

**缺点：**
- 1、SQL语句的编写工作量较大，尤其字段多、关联表多时，对开发人员编写SQl语句的功底有一定的要求。
- 2、SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库