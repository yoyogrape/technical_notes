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
配置示例：
spring :
  redis:
    host: 127.0.0.1
    database: 0
    port: 6379
    password: sojson.com
    pool:
      max-active: 8
      max-idle: 8
      max-wait: -1
      min-idle: 0
    timeout: 1000
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


https://github.com/meiwanmeiliao/redisTemplate-demo/blob/master/config/src/main/java/com/zl/RedisConfig.java