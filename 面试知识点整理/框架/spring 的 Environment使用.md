# spring 的 Environment使用

Environment相关使用分为两部分，一个是Properties属性的使用一个是Profile使用。

#### 我们先来看看Properties属性的使用：

- 引入properties配置文件：

  ```javajava
  <context:property-placeholder location="classpath:db.properties" />
  ```

- 使用的方式

  - ​	

    ```java
    @Autowired
    private Environment environment;
    
    public void getProperty() {
      environment.getProperty("jdbc.driverClassName");
    }
    ```

- 类似使用注解的方式如下：

  - ```java
    @Configuration
    
    @PropertySource("classpath:db.properties")
    
    public class TestProperties {
    
    	@Autowired
    
    	Environment env;
    
    	public void getProperty() {
    
    		environment.getProperty("jdbc.driverClassName");
    
    	}
    }
    ```

#### profile的使用是为了能以最简单的方式切换配置文件，通常是简化多个环境不同配置的复杂，下面我们来看看使用方式：

```java
<beans profile="test">

	<context:property-placeholder location="/WEB-INF/test.properties" />

</beans>


<beans profile="beta">

	<context:property-placeholder location="/WEB-INF/beta.properties" />

</beans>
```

激活指定profile的三种方式：

- 代码指定

  - ```java
    ConfigurableEnvironment.setActiveProfiles("test")
    ```

- 配置文件指定

  - ```java
    <init-param>
    
      <param-name>spring.profiles.active</param-name>
    
      <param-value>beta</param-value>
    
    </init-param>
    ```

- Tomcat启动参数指定

  - ```java
    JAVA_OPTS="-Dspring.profiles.active=test"
    ```