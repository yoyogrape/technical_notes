# springboot 整合redis
redis中文网：`http://redis.cn/`
### 第一节 docker安装redis

1）连接docker中国，下载命令：
```
docker pull registry.docker-cn.com/library/镜像名称
```
2）启动redis
```
docker run -d -p 6379:6379 --name=myredis  registry.docker-cn.com/library/redis
```
4）工具连接 redis  
RedisDesktopManager工具进行连接

5）进行简单命令测试

### 第二节 项目中引入redis
1）添加pom文件依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
```

2）yml文件中配置redis

```
spring:
  redis:
    host: 192.168.0.104
```
### 第三节 项目中使用redis

1）注入两个操作redis对象：

```
@Autowired
private RedisTemplate redisTemplate;

@Autowired
private StringRedisTemplate stringRedisTemplate;

```

2）对redis常见数据类型进行操作测试

```
 * Redis常见的五大数据类型
 *  String（字符串）、List（列表）、Set（集合）、Hash（散列）、ZSet（有序集合）
 *  stringRedisTemplate.opsForValue()[String（字符串）]
 *  stringRedisTemplate.opsForList()[List（列表）]
 *  stringRedisTemplate.opsForSet()[Set（集合）]
 *  stringRedisTemplate.opsForHash()[Hash（散列）]
 *  stringRedisTemplate.opsForZSet()[ZSet（有序集合）]
```

3）对字符串进行存储
```
Integer append = stringRedisTemplate.opsForValue().append("msg", "hello");
String msg = stringRedisTemplate.opsForValue().get("msg");
```
4）对集合类型存储
```
stringRedisTemplate.opsForList().leftPush("myList", "1");
stringRedisTemplate.opsForList().leftPush("myList", "3");
```
5）对对象存储
```
//存在缓存中是java序列化器进行序列化
redisTemplate.opsForValue().set("emp-songjn", JSON.toJSONString(emp));
Object o = redisTemplate.opsForValue().get("emp-songjn");

```

```
//使用自定义json序列化器存到redis中
empRedisTemplate.opsForValue().set("emp-songjn", emp);
Employee employee = empRedisTemplate.opsForValue().get("emp-songjn");

需要进行序列化器的配置
@Configuration
public class MyRedisConfig {
    @Bean
    public RedisTemplate<Object, Employee> redisTemplate(RedisConnectionFactory redisConnectionFactory) throws UnknownHostException {
        RedisTemplate<Object, Employee> template = new RedisTemplate();
        template.setConnectionFactory(redisConnectionFactory);
        Jackson2JsonRedisSerializer<Employee> employeeJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Employee>(Employee.class);
        template.setDefaultSerializer(employeeJackson2JsonRedisSerializer);
        return template;
    }
}
```
### 第四节 redis作为缓存
```
三、整合redis作为缓存
 * Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。
 * 	1、安装redis：使用docker；
 * 	2、引入redis的starter
 * 	3、配置redis
 * 	4、测试缓存
 * 		原理：CacheManager===Cache 缓存组件来实际给缓存中存取数据
 *		1）、引入redis的starter，容器中保存的是 RedisCacheManager；
 *		2）、RedisCacheManager 帮我们创建 RedisCache 来作为缓存组件；RedisCache通过操作redis缓存数据的
 *		3）、默认保存数据 k-v 都是Object；利用序列化保存；如何保存为json
 *   			1、引入了redis的starter，cacheManager变为 RedisCacheManager；
 *   			2、默认创建的 RedisCacheManager 操作redis的时候使用的是 RedisTemplate<Object, Object>
 *   			3、RedisTemplate<Object, Object> 是 默认使用jdk的序列化机制
 *      4）、自定义CacheManager；
 ```