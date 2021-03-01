# redisTemplate的配置和使用

#### 配置

- 依赖

依然是采用Jedis进行连接池管理，因此除了引入 `spring-data-redis`之外，再加上jedis依赖，pom文件中添加

```xml
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.8.4.RELEASE</version>
</dependency>


<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.9.0</version>
</dependency>
```

如果需要指定序列化相关参数，也可以引入jackson，本篇为简单入门级，就不加这个了

- 配置文件

准备redis相关的配置参数，常见的有host, port, password, timeout...，下面是一份简单的配置，并给出了相应的含义

```yml
redis.hostName=127.0.0.1
redis.port=6379
redis.password=https://blog.hhui.top

# 连接超时时间
redis.timeout=10000

#最大空闲数
redis.maxIdle=300

#控制一个pool可分配多少个jedis实例,用来替换上面的redis.maxActive,如果是jedis 2.4以后用该属性
redis.maxTotal=1000

#最大建立连接等待时间。如果超过此时间将接到异常。设为-1表示无限制。
redis.maxWaitMillis=1000

#连接的最小空闲时间 默认1800000毫秒(30分钟)
redis.minEvictableIdleTimeMillis=300000

#每次释放连接的最大数目,默认3
redis.numTestsPerEvictionRun=1024

#逐出扫描的时间间隔(毫秒) 如果为负数,则不运行逐出线程, 默认-1
redis.timeBetweenEvictionRunsMillis=30000

#是否在从池中取出连接前进行检验,如果检验失败,则从池中去除连接并尝试取出另一个
redis.testOnBorrow=true

#在空闲时检查有效性, 默认false
redis.testWhileIdle=true
```

redis密码请一定记得设置，特别是在允许远程访问的时候，如果没有密码，默认端口号，很容易就被是扫描注入脚本，然后开始给人挖矿（亲身经历...）

#### 使用和测试

根据一般的思路，首先是得加载上面的配置，创建redis连接池，然后再实例化RedisTemplate对象，*最后持有这个实力开始各种读写操做*

- 配置类

使用JavaConfig的方式来配置，主要是两个Bean，读取配置文件设置各种参数的`RedisConnectionFactory`以及预期的`RedisTemplate`

    @Configuration
    @PropertySource("classpath:redis.properties")
    public class RedisConfig extends JCacheConfigurerSupport {
        @Autowired
        private Environment environment;
    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        JedisConnectionFactory fac = new JedisConnectionFactory();
        fac.setHostName(environment.getProperty("redis.hostName"));
        fac.setPort(Integer.parseInt(environment.getProperty("redis.port")));
        fac.setPassword(environment.getProperty("redis.password"));
        fac.setTimeout(Integer.parseInt(environment.getProperty("redis.timeout")));
        fac.getPoolConfig().setMaxIdle(Integer.parseInt(environment.getProperty("redis.maxIdle")));
        fac.getPoolConfig().setMaxTotal(Integer.parseInt(environment.getProperty("redis.maxTotal")));
        fac.getPoolConfig().setMaxWaitMillis(Integer.parseInt(environment.getProperty("redis.maxWaitMillis")));
        fac.getPoolConfig().setMinEvictableIdleTimeMillis(
                Integer.parseInt(environment.getProperty("redis.minEvictableIdleTimeMillis")));
        fac.getPoolConfig()
                .setNumTestsPerEvictionRun(Integer.parseInt(environment.getProperty("redis.numTestsPerEvictionRun")));
        fac.getPoolConfig().setTimeBetweenEvictionRunsMillis(
                Integer.parseInt(environment.getProperty("redis.timeBetweenEvictionRunsMillis")));
        fac.getPoolConfig().setTestOnBorrow(Boolean.parseBoolean(environment.getProperty("redis.testOnBorrow")));
        fac.getPoolConfig().setTestWhileIdle(Boolean.parseBoolean(environment.getProperty("redis.testWhileIdle")));
        return fac;
    }
     
    @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, String> redis = new RedisTemplate<>();
        redis.setConnectionFactory(redisConnectionFactory);
        redis.afterPropertiesSet();
        return redis;
    }
    }
- 测试和使用

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = {RedisConfig.class})
    public class RedisTest {
    @Autowired
    private RedisTemplate<String, String> redisTemplate;
    
    @Test
    public void testRedisObj() {
        Map<String, Object> properties = new HashMap<>();
        properties.put("123", "hello");
        properties.put("abc", 456);
    
        redisTemplate.opsForHash().putAll("hash", properties);
    
        Map<Object, Object> ans = redisTemplate.opsForHash().entries("hash");
        System.out.println("ans: " + ans);
    }
    }
#### redistemplate使用姿势

- opsForXXX

  针对不同的数据结构(String, List, ZSet, Hash）读封装了比较使用的调用方式 `opsForXXX`

- 直接使用execute

    @Test
    public void testRedis() {
        String key = "hello";
        String value = "world";
        redisTemplate.execute((RedisCallback<Void>) con -> {
            con.set(key.getBytes(), value.getBytes());
            return null;
        });
    String asn = redisTemplate.execute((RedisCallback<String>) con -> new String(con.get(key.getBytes())));
    System.out.println(asn);

    String hkey = "hKey";
    redisTemplate.execute((RedisCallback<Void>) con -> {
        con.hSet(hkey.getBytes(), "23".getBytes(), "what".getBytes());
        return null;
    });
     
    Map<byte[], byte[]> map = redisTemplate.execute((RedisCallback<Map<byte[], byte[]>>) con -> con.hGetAll(hkey.getBytes()));
    for (Map.Entry<byte[], byte[]> entry : map.entrySet()) {
        System.out.println("key: " + new String(entry.getKey()) + " | value: " + new String(entry.getValue()));
    }
    }
- 区别

opsForXXX 的底层，就是通过调用execute方式来做的，其主要就是封装了一些使用姿势，定义了序列化，使用起来更加的简单和便捷；