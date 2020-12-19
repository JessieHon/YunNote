# spring全家桶

- tomcat工作原理https://zhuanlan.zhihu.com/p/248426114

- spring boot、spring和spring cloud区别

  - springboot
    - 简化了项目的配置，方便部署
    - 内嵌了web容器，可以直接运行
  - 总结：Spring boot 是 Spring 的一套快速配置脚手架，可以基于spring boot 快速开发单个微服务，Spring Cloud是一个基于Spring Boot实现的云应用开发工具；

- 前置知识：
  
  - jetty：基于java的web容器，可以迅速为一些独立运行的java应用提供网络和web连接
  - daemon是一段连续运行的程序，用于处理计算机系统希望接收到的阶段性的服务需求。daemon程序段将请求提交给其他合适的程序（或者进程）。网络上每个页面的服务器都有一个HTTPD或是超文本传输协议daemon，持续地等待从网络客户端及其用户发送来的请求
  
- servelet生命周期

  - Servlet 初始化后调用 **init ()** 方法。

    - init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。因此，它是用于一次性初始化，就像 Applet 的 init 方法一样。

      Servlet 创建于用户第一次调用对应于该 Servlet 的 URL 时，但是您也可以指定 Servlet 在服务器第一次启动时被加载。

      当用户调用一个 Servlet 时，就会创建一个 Servlet 实例，每一个用户请求都会产生一个新的线程，适当的时候移交给 doGet 或 doPost 方法。init() 方法简单地创建或加载一些数据，这些数据将被用于 Servlet 的整个生命周期。

  - Servlet 调用 **service()** 方法来处理客户端的请求。

    - service() 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。

      每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP  请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

    - service() 方法由容器调用，service 方法在适当的时候调用 doGet、doPost、doPut、doDelete  等方法。所以，您不用对 service() 方法做任何动作，您只需要根据来自客户端的请求类型来重写 doGet() 或 doPost() 即可。

  - Servlet 销毁前调用  **destroy()** 方法

    - destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。

  - 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的

    - 在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收

- spring mvc的配置

  - 配置组件扫描--**必配**
    - 组件扫描会扫描包下的所有的Controller类
    - ​    `<context:component-scan base-package="controller" />`
  - 配置MVC注解扫描--**必配**
    - 和组件扫描搭配，相当于同时配置了HandlerMapping和Controller
    - `<mvc:annotation-driven />`
  - 配置视图解析器--**选配**
    - 视图解析器是为了当html，jsp等前端文件放到WEB-INF/的路径下时，浏览器不能直接访问，就需要使用视图解析器来访问相应的前端文件。
    - ```
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      	<property name="prefix" value="/WEB-INF/" /><!-- 前缀 -->
      	<property name="suffix" value=".jsp" /><!-- 后缀 -->
      </bean>
      ```
  - 配置拦截器--**选配**
    
    - 拦截器是拦截tomcat容器和spring容器之间的交互信息，主要是用于验证用户在访问该网页时是否登陆

- springboot框架是怎么简化spring开发的？
  
  - SpringBootApplication的用法：这个注解一般放在项目的一个启动类上，用来把启动类注入到容器中，用来定义容易扫描的范围，用来加载classpath环境中的一些bean
    - springbootApplication注解主要包装了springbootAutoConfiguration和enableAutoConfigration和componentScan三个注解
    - springbootconfiguration注解主要用来把bean注入到容器，包装了configuration注解，configuration注解里面又封装了component注解，因此springbootconfiguration就有了component注解的功能，就是把一个bean注入到容器中
    - enableAutoConfiguration这个注解将基于已经添加依赖自动添加相应的配置，如果spring-boot-starter-web已经添加了tomcat和springmvc，这个注解将自动假设现在在开发一个web应用程序并添加相应的spring配置，可以自由挑选starter依赖之外的jar包，通过这个注解springboot自动配置这个应用程序
  - @SpringBootApplication注解默认只扫描该注解注解类所在的包，可以通过该注解中的scanBasePackage属性显式的指定要扫描的包，可以用springbootApplication中的exclude属性显式指定要排除的类，或者excludeName属性显式指定要排除的类的名字
  
- springboot启动原理
  - 为什么项目启动之后可以一直跑？
    - 因为启动了线程池，一直有daemon线程在运行，jvm定义要等所有用户线程都运行完之后才可以退出
  - springboot默认应用tomcat作为web容器，可以通过依赖使用jetty，是怎么做到切换的？
    - servletwebServerFactoryConfiguration里面：如果tomcat依赖存在加用tomcat，不然就按照顺序寻找其他web容器，找到了就返回这个bean作为servelet的工厂类
  - 
  - springboot通过main函数是怎么启动web容器的呢？
    - springApplication.run会创建spring的应用上下文，默认是AnnotationConfigServletWebServerApplicationContext
    - 首先会加载spring开天辟地的五个bean
    - 然后初始化各种bean工厂
    - SpringBoot在ServletWebServerApplicationContext中重载了onRefresh方法，除了以前Spring默认的onRefresh方法外还增加了createWebServer方法，在这个方法中对Web容器进行了初始化工作
    - 容器启动的基本原理就是创建个线程池和网络套接字。用线程去处理套接字读写的内容
  
- spring boot的启动过程：springApplication里面封装了spring应用的启动流程，对用户完全透明
  
  - 借助于Spring框架**原有**的一个工具类：**SpringFactoriesLoader**的支持，@EnableAutoConfiguration可以智能的自动配置功效才得以大功告成！
  
    SpringFactoriesLoader属于Spring框架私有的一种扩展方案，其主要功能就是从**指定的配置文件META-INF/spring.factories**加载配置,**加载工厂类**。
  
    SpringFactoriesLoader为Spring工厂加载器，该对象提供了loadFactoryNames方法，入参为factoryClass和classLoader即需要传入**工厂类**名称和对应的类加载器，方法会根据指定的classLoader，加载该类加器搜索路径下的指定文件，即spring.factories文件；
  
    传入的工厂类为接口，而文件中对应的类则是接口的实现类，或最终作为实现类。
  
- spring mvc的工作流程
  - 客户端请求到DispatcherServlet
  - DispatcherServelet控制器通过一个或者多个handlerMapping找到处理请求的controller
  - DispatcherServelet将请求提交到controller
  - controller调用业务逻辑处理完之后返回modelAndView
  - DispatcherServlet寻找一个或者多个viewResolver视图解析器，找到modelAndView执行的视图
  - 视图负责将结果显示在客户端
  
- SpringCloud组件

  - 服务发现——Netflix Eureka
  - 客服端负载均衡——Netflix Ribbon
  - 断路器——Netflix Hystrix
  - 服务网关——Netflix Zuul
  - 分布式配置——Spring Cloud Config
  
- BeanPostProcessor后置处理器

  - 如果我们想在Spring容器中完成bean实例化、配置以及其他初始化方法前后要添加一些自己逻辑处理。我们需要定义一个或多个BeanPostProcessor接口实现类，然后注册到Spring IoC容器中。
  - 后置处理器的postProcessorBeforeInitailization方法是在bean实例化，依赖注入之后及自定义初始化方法(例如：配置文件中bean标签添加init-method属性指定Java类中初始化方法、
    @PostConstruct注解指定初始化方法，Java类实现InitailztingBean接口)之前调用
  - 后置处理器的postProcessorAfterInitailization方法是在bean实例化、依赖注入及自定义初始化方法之后调用