# springboot缓存的使用

### 第一节注解说明
**1）@EnableCaching**

>添加在主控制类上，开启缓存

**2）@Cacheable**
>添加在方法上，将查询的结果添加进缓存中，第二次查询直接缓存中获取结果

**属性说明：**
```  
cacheNames/value：指定缓存组件的名字;将方法的返回结果放在哪个缓存中，是数组的方式，可以指定多个缓存；

key：缓存数据使用的key；可以用它来指定。默认是使用方法参数的值  1-方法的返回值
                     编写SpEL； #id;参数id的值   #a0  #p0  #root.args[0]
                     getEmp[2]

keyGenerator：key的生成器；可以自己指定key的生成器的组件id
                  key/keyGenerator：二选一使用;

cacheManager：指定缓存管理器；或者cacheResolver指定获取解析器

condition：指定符合条件的情况下才缓存；
     condition = "#id>0"
     condition = "#a0>1"：第一个 参数的值大于1的时候才进行缓存
     
     
unless:否定缓存；当unless指定的条件为true，方法的返回值就不会被缓存；可以获取到结果进行判断
    unless = "#result == null"
    unless = "#a0==2":如果第一个参数的值是2，结果不缓存；
    
sync：是否使用异步模式
```

**3）@CachePut**
>先调用目标方法，然后将目标方法的返回值放到缓存中

>注意：key要保证和缓存数据中的key保持一致，否则更新失败。
```
key = "#employee.id":使用传入的参数的员工id；
key = "#result.id"：使用返回后的id
@Cacheable的key是不能用#result
```
        

**4）@CacheEvict**

>缓存清除
``` 
*  key：指定要清除的数据
*  allEntries = true：指定清除这个缓存中所有的数据
*  beforeInvocation = false：缓存的清除是否在方法之前执行
*      默认代表缓存清除操作是在方法执行之后执行;如果出现异常缓存就不会清除
*
*  beforeInvocation = true：
*      代表清除缓存操作是在方法运行之前执行，无论方法是否出现异常，缓存都清除
```

**5）@Caching** 
>定义复杂的缓存规则
```
@Caching(
         cacheable = {
             @Cacheable(/*value="emp",*/key = "#lastName")
         },
         put = {
             @CachePut(/*value="emp",*/key = "#result.id"),
             @CachePut(/*value="emp",*/key = "#result.email")
         }
    )
```

**6）@CacheConfig(cacheNames = "emp")**
>将公共方法提取出来
```
//@CacheConfig(cacheNames = "emp")
@Service
public class EmpService {
```
### 第二节  运行原理

```
原理：
     *   1、自动配置类；CacheAutoConfiguration
     *   2、缓存的配置类
     *   org.springframework.boot.autoconfigure.cache.GenericCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.JCacheCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.EhCacheCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.HazelcastCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.InfinispanCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.CouchbaseCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.RedisCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.CaffeineCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.GuavaCacheConfiguration
     *   org.springframework.boot.autoconfigure.cache.SimpleCacheConfiguration【默认】
     *   org.springframework.boot.autoconfigure.cache.NoOpCacheConfiguration
     *   3、哪个配置类默认生效：SimpleCacheConfiguration；
     *
     *   4、给容器中注册了一个CacheManager：ConcurrentMapCacheManager
     *   5、可以获取和创建ConcurrentMapCache类型的缓存组件；他的作用将数据保存在ConcurrentMap中；

```

### 第三节  运行流程
```
运行流程：
     *   @Cacheable：
     *   1、方法运行之前，先去查询Cache（缓存组件），按照cacheNames指定的名字获取；
     *      （CacheManager先获取相应的缓存），第一次获取缓存如果没有Cache组件会自动创建。
     *   2、去Cache中查找缓存的内容，使用一个key，默认就是方法的参数；
     *      key是按照某种策略生成的；默认是使用keyGenerator生成的，默认使用SimpleKeyGenerator生成key；
     *          SimpleKeyGenerator生成key的默认策略；
     *                  如果没有参数；key=new SimpleKey()；
     *                  如果有一个参数：key=参数的值
     *                  如果有多个参数：key=new SimpleKey(params)；
     *   3、没有查到缓存就调用目标方法；
     *   4、将目标方法返回的结果，放进缓存中
     *
     *   @Cacheable标注的方法执行之前先来检查缓存中有没有这个数据，默认按照参数的值作为key去查询缓存，
     *   如果没有就运行方法并将结果放入缓存；以后再来调用就可以直接使用缓存中的数据；
     *
     *   核心：
     *      1）、使用CacheManager【ConcurrentMapCacheManager】按照名字得到Cache【ConcurrentMapCache】组件
     *      2）、key使用keyGenerator生成的，默认是SimpleKeyGenerator
     *
```

### 第四节 使用示例
```
第一步：启动类开启缓存
@MapperScan(value = "com.songjn.springboot_cache.mapper")
@EnableCaching
@SpringBootApplication
public class SpringbootCacheApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootCacheApplication.class, args);
    }
}

第二步：service层使用缓存
//@CacheConfig(cacheNames = "emp")//将公共属性提取出来
@Service
public class EmpService {
    @Resource
    private EmployeeMapper empDao;

    //将查询到的数据进行缓存
    @Cacheable(value = "emp"/*, keyGenerator = "myKeyGenerator", condition = "#id==2"*/)
    public String findEmpById(Integer id) {
        System.out.println("---EmpService---findEmpById()---");
        Employee emp = empDao.getEmpById(id);
        String empJson = JSON.toJSONString(emp);
        System.out.println("emp：：：" + emp);
        return empJson;
    }

    /**
     * 注意：key要保证和缓存数据中的key保持一致，否则更新失败。
     * 应该是更新后的员工；
     * key = "#employee.id":使用传入的参数的员工id；
     * key = "#result.id"：使用返回后的id
     *
     * @param employee
     * @return
     * @Cacheable的key是不能用#result
     */
    @CachePut(value = "emp", key = "#employee.id")
    public String updateEmp(Employee employee) {
        System.out.println("---EmpService---updateEmp()---");
        Integer integer = empDao.updateEmp(employee);
        System.out.println("int=" + integer);
        return JSON.toJSONString(employee);
    }

    @CacheEvict(value = "emp", key = "#id")
    public String delEmp(Integer id) {
        System.out.println("---EmpService---delEmp()---");
        Integer integer = empDao.delEmp(id);
        System.out.println("int=" + integer);
        String response = integer > 0 ? "success" : "失败";
        return response;
    }
    // @Caching 定义复杂的缓存规则
    @Caching(
            cacheable = {
                    @Cacheable(value="emp",key = "#lastName")
            },
            put = {
                    @CachePut(value="emp",key = "#result.id"),
                    @CachePut(value="emp",key = "#result.email")
            }
    )
    public Employee getEmpByLastName(String lastName){
        System.out.println("---EmpService---getEmpByLastName()---");
        Employee emp = empDao.getEmpByLastName(lastName);
        return emp;
    }
}

```
