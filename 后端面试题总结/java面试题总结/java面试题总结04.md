

# 31、线程池复用的原理
  - 线程池将==线程==和==任务==进行解耦，线程是线程，任务是任务，摆脱了之前通过Thread创建线程时的一个线程必须对应一个任务的限制。

  - 在线程池中，同一个线程可以从==阻塞队列==中不断获取新任务来执行，其核心原理在于==线程池对Thread进行了封装==，并不是每次执行任务都会调用Thread.start()来创建线程，而是让每个线程去执行一个“==循环任务==”，在这个“循环任务”中不停检查是否有任务需要被执行，也就是调用任务中的run方法，将run方法当成一个普通的方法执行，通过这种方式只使用固定的线程就将所有任务的run方法串联起来

# 32、spring是什么？

轻量级的开源的J2EE框架，它是一个==容器框架==，用来装javabean（java对象），中间层框架（万能胶）可以起一个连接作用，比如说把Structs和hibernate粘合在一起运用，可以让我们的企业开发更快，更简洁

**Spring是一个轻量级的控制反转（IOC)和面向切面（AOP）的容器框架**

- 从大小与开销两方面而言Spring都是轻量级的。

- 通过控制反转（IOC）的技术达到松耦合的目的。

- 提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性的开发。

- 包含并管理应用对象（Bean)的配置和生命周期，这个意义上是一个容器

- 将简单的组件配置，组合成为复杂的应用，这个意思上是一个框架。

# 33、对Aop的理解
- 系统是由许多不同的组件所组成的每一个组件各负责一块特定功能。除了实现自身核心功能之外，这些组件还经常承担着额外的职责。例如日志，事务管理和安全这样的核心服务经常融入到自身具有核心业务逻辑的组件中去。这些系统服务经常被称为==横切关注点==，因为它们会跨越系统的多个组件。
- 当我们需要为分散的对象引入公共行为的时候，O0P则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系，例如日志功能。
- 
- 日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系
- 
- 在OOP设计中，它导致了大量代码的重复，而不利于各个模块的复用
- 
- ==AOP==：将程序中的交叉业务逻辑（比如安全、日志、事务等），==封装成了一个切面，然后注入到目标对象（具体业务逻辑）中去==，AOP可以对某个对象或某些对象的==功能进行增强==，比如对象中的方法进行增强，可以在执行某个==方法之前==额外的做一些事情，在某个==方法执行之后==额外的做一些事情




# 34、对IOC的理解
容器概念、控制反转、依赖注入

**IOC容器**：
- 实际上就是个map（key，value),里面存的是各种对象（在xml里配置的bean节点，@repository、@service、@controller、@component），在项目启动时的时候会读取配置文件里面的bean节点，根据全限定类名使用反射创建对象放到map里，扫描到打上上述注解的类还是通过反射创建对象放到map里。

- 这个时候map里就有各种对象了，接下来我们在代码里需要用到里面的对象时，再通过==DI注入==（autowired、resource等注解，xml里面bean节点内的ref属性，项目启动时候会读取xml节点ref属性更具DI注入，也会扫描这些注解，根据类型或DI注入，id就是对象名）


**控制反转**
- 没有引入IOC容器之前，对象A依赖与对象B，那么对象A在初始化或者在运行到某一点的时候，自己必须主动去创建对象B或者使用已经创建的对象B。无论是创建还是使对象B，控制权都在自己手上。

- 引入IOC容器之后，对象A与对象B之间失去了直接关系，当对象A运行到需要对象B的时候，IOC容器会主动去创建一个对象B注入到对象A需要的地方

- 通过前后的对比，不难看出：对象A获得依赖对象B的过程由主动行为变为了被动行文，==控制权颠倒过来了==，这就是“==控制反转==”这个名称的由来

全部对象的控制权全部上缴给“==第三方“IOC容器==、所以，IOC容器成了整个系统的关键核心，它起到了一种类似“==粘合剂==” 的作用，把系统中的所有对象粘合在一起发挥作用，如果没有这个“粘合剂”，对象与对象之间会彼此失去联系，这就是有人把IOC容器比喻成“粘合剂”的由来

**依赖注入**

- 获得依赖对象的过程被反转了。控制被反转之后，获得对象依赖对象的过程由==自身管理变为了由IOC容器主动注入==。依赖注入是实现IOC的方法，就是由IOC容器在运行期间，==动态地将某种依赖关系注入到对象中==。
# 35、BeanFactory和ApplicationContext有什么什么区别？

ApplicationContext是BeanFactory的子接口

ApplicationContext提供了更完整的功能：
- 继承了MessageSource，因此支持国际化

- 统一的资源文件访问方式

- 提供在监听器中注册bean的事件

- 同时加载多个配置文件

- 载入多个（有继承关系）上下文，使得每一个上下文都专注与一个特定的层次，比如应用的web层

`BeanFactory`采用的是==延迟加载==形式来注入Bean的，即只有在==使用到某个Bean时==（调用getBean())，才对该Bean进行加载实例化。这样、我们就不能发现一些存在的Spring配置问题。如果Bean的某一个属性没有注入，BeanFactory加载后，直至第一次使用调用getBean()方法才会抛出异常

`ApplicationContext`，它是==在容器启动时，一次性创建了所有的Bean==。这样，在容器启动时，我们就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入。`ApplicationContext`启动后预载入所有的单实例Bean，确保当你需要的时候，你就不用在等待，因为他们已经创建好了。

相对于基本的`BeanFactory`,`ApplicationContext`唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。

`BeanFactory`通常以编程的方式被创建，`ApplicationContext`还能以声明的方式创建，如使用`ContextLoader`

`BeanFactory`和`ApplicationContext`都支持`BeanPostProcessor`、`BeanFactoryPostProcessor`的使用，但两者之间的区别是：`BeanFactory`需要手动注册，而`ApplicationContext`则是自动注册


# 36、简述spring bean的生命周期
- 1、解析类得到的`BeanDefinition`
- 2、如果有多个构造方法后，则要推断构造方法
- 3、确定好构造方法后，进行实例化得到一个对象
- 4、对对象中的家里`@Autowired`注解的属性进行属性填充
- 5、回调Aware方法，比如`BeanNameAware`，`BeanFactoryAware`
- 6、调用`BeanPostProcessor`的初始化前的方法
- 7、调用初始化方法
- 8、调用`BeanPostProcessor`的初始化后的方法，在这里进行AOP
- 9、如果当前创建的bean是单例的则会把bean放入单例池
- 10、使用bean
- 11、Spring容器关闭时调用`DisposableBean`中destory()方法

# 37、spring支持的几种bean作用域
- **singleton**：默认，每个容器中==只有一个bean的实例==，单例的模式由BeanFactory自身来维护。该对象的生命周期是与Spring IOC容器一致的（但是第一次被注入时才会创建）

- **prototype**：为每一个bean请求提供一个实例。在每次注入时都会创建一个新的对象

- **request**：bean被定义为在每个HTTP请求中创建一个单例对象，也就是说在单个请求中都会复用这一个单例对象

- **session**：与request范围类似，确保每个session中有一个bean的实例，在sesssion过期后，bean会随之失效

- **application**：bean被定义为在servletContext的生命周期中复用一个单例对象。

- **websocket**：bean被定义为在websocket的生命周期中复用一个单例对象。

**global-session**：全局作用域，global-session和Portlet应用相关。当你的应用部署在Portlet容器中工作时，它包含很多portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这全局变量需要存在`global-session`中。全局作用域与servlet中的session作用域效果相同。

# 38、Spring框架中的单例Bean是线程安全的吗
Spring中的Bean默认是==单例模式==的，框架并没有对bean进行多线程的封装处理。

如果Bean是有状态的 那就需要开发人员自己来进行线程安全的保证，最简单的方法就是改变bean的作用域 把”singleton"改为“protopyte"这样每次请求Bean就相当于是 new Bean（）这样就可以保证线程安全了。
- 有效状态就是有数据存储功能
- 无效状态就是不会保存数据  controller、service和dao层本身并不是线程安全的，只是如果只是调用里面的方法，而且多线程调用一个实例的方法，会在内存中复制变量，这是自己的线程的工作内存，是安全的。

Dao会操作数据库Connection、Connection是带有状态的，比如说数据库事务、spring的事务管理器使用

Threadlocal为不同线程维护了一套独立的connection副本，保证线程之间不会相互影响（spring是如何保证事务获取同一个Connection的)

不要在bean中声明任何有状态的实例变量或类变量，如果必须如此，那么就使用Threadlocal把变量变为私有线程有的，如果bean的实例变量或类变量需要在多个线程之间共享，那么就只能使用synchronized、lock、CAS等这些实现线程同步的方法了。
# 39、spring框架中使用了哪些设计模式及应用场景

**简单工厂**：由一个工厂类根据传入的参数，动态决定应该创建哪一个产品类

- Spring中的BeaFactory就是简单工厂模式的体现，根据传入一个==唯一的标识符==来获得Bean对象，但是否是在传入==参数后==创建还是传入==参数前==创建这个要根据具体情况来定

**工厂方法：**

- 实现了FactoryBean接口的bean是一类叫做factory的bean，其特点是，spring会再使用getBean()调用获得该bean时，会自动调用该bean的getObject()方法，所以返回的不是factory这个bean，而是这个bean.getObject()方法的返回值。

**单例模式**：保证一个类仅有一个实例，并提供了一个访问它的==全局访问点==

- spring对单例的实现：spring中的单例模式完成了后半句话，即提供了全局的访问点BeanFactory，但没有从构造器级别去控制单例，这是因为spring管理的时任意的java对象

**适配器模式**

- spring定义了一个适配接口，使得每一个Controller有一种对应的适配器实现类，让适配器代替controller执行相应的方法，这样在扩展controller时，只需要增加一个适配器类就完成了spirngmvc的扩展了

**装饰模式**：动态地给一个对象添加一些额外的职责。就是增加功能来说，Decorator模式相比生成子类更为灵活。
- spring中用到的包装器模式在类名上有两种表现，一种是类名中含有`wrapper`，另一种是类名中含有`Decorator`。

**动态代理：**
- 切面在应用运行时的时刻被织入，在织入切面时，AOP容器会为目标对象创建动态的创建一个代理对象。SpringAOP就是以这种方式织入切面的。
- 织入：把切面应用到目标对象并创建新的代理对象的过程

**观察者模式：**
- spring的事件驱动模型使用的是：观察者模式，Spring中obserber模式常用的地方是listener的实现

**策略模式：**
- spring框架的资源访问Resource接口，该接口提供了更强的资源访问能力，Spring框架本身大量使用了Resource接口来访问底层资源
# 40、spring事务的实现方式原理以及隔离级别
在使用Spring框架时，可以有两种使用事务的方式，一种是===编程式==的，一种是==申明式==的，@Transactiona注解就是申明式的。

首先，事务这个概念是数据库层面的，Spring只是基于数据库中的事务进行了扩展，以及提供一些能让程序员更加方便操作事务的方式。

比如我们可以提通过在某个方法上增加了`@Transactionanl`注解，就可以开启事务，这个方法中所有的的sql都会在一个事务中执行，统一超过或失败。

在一个方法上加了`@Transactional`注解后，Spring会基于这个类生成一个代理对象，会将这个代理对象作为bean，当在使用这个代理对象的方法时，如果这个方法存在`@Transactional`注解，那么代理逻辑就会先把事务的自动提交设置为false，然后再去执行原本的业务逻辑方法，如果执行业务逻辑方法没有出现异常，那么代理逻辑中就会将事务进行提交，如果执行业务逻辑方法出现了异常，那么会将事务进行回滚。

当然，针对哪些异常回滚事务是可以配置的，可以利用`@Transactional`注解中的rollbackFor属性进行配置，默认情况下会对`RuntimeException`进行回滚。

spring事务隔离级别就是数据库的隔离级别：外加一个默认级别
- read uncommitted（未提交读）
- read committed （提交读、不可重复读）
- repeatable read （可重复读）
- serializable （可串行化）

数据库的配置隔离级别是 Read Committed，而spirng配置的隔离级别是Repeatable Read，请问这时隔离级别是以哪个为准？

以Spring配置的为准，如果spring设置的隔离级别数据库不支持，效果取决于数据库。