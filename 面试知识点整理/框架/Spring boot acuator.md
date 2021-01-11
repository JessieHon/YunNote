# Spring boot acuator

https://www.jianshu.com/p/c6d172577c3d	

微服务的特点决定了功能模块的部署是分布式的，大部分功能模块都是运行在不同的机器上，彼此通过服务调用进行交互，前后台的业务流会经过很多个微服务的处理和传递，出现了异常如何快速定位是哪个环节出现了问题？

#### Acuator监控

​	Spring Boot使用“习惯优于配置的理念”，采用包扫描和自动化配置的机制来加载依赖jar中的Spring bean,不需要任何Xml配置，就可以实现Spring的所有配置。虽然这样做能让我们的代码变得非常简洁，但是整个应用的实例创建和依赖关系等信息都被离散到了各个配置类的注解上，这使得我们分析整个应用中资源和实例的各种关系变得非常的困难。

​	Actuator是Spring Boot提供的对应用系统的自省和监控的集成功能，可以查看应用配置的详细信息，例如自动化配置信息、创建的Spring beans以及一些环境属性等。

​	Actuator监控只需要添加以下依赖就可以完成

``

```xml
<dependencies>
    <!-- 如果使用http调用的方式，还需要这个依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- 必须的 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!-- 安全 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

​	为了保证actuator暴露的监控接口的安全性，需要添加安全控制的依赖spring-boot-start-security依赖，访问应用监控端点时，都需要输入验证信息。Security依赖，可以选择不加，不进行安全管理，但不建议这么做。

#### Acuator的rest接口

​	Actuator监控分成两类：原生端点和用户自定义端点；自定义端点主要是指扩展性，用户可以根据自己的实际应用，定义一些比较关心的指标，在运行期进行监控

原生端点是在应用程序里提供众多 Web 接口，通过它们了解应用程序运行时的内部状况。原生端点又可以分成三类：

- 应用配置类：可以查看应用在运行期的静态信息：例如自动配置信息、加载的springbean信息、yml文件配置信息、环境信息、请求映射信息；

- 度量指标类：主要是运行期的动态信息，例如堆栈、请求连、一些健康指标、metrics信息等；

- 操作控制类：主要是指shutdown,用户可以发送一个请求将应用的监控功能关闭。





​	