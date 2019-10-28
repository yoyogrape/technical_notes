# springboot常用注解总结

### Application启动类常用注解说明：

```
@ImportResource(locations = {"classpath:xmldata/bean.xml"})
@SpringBootApplication
@EnableAsync
@EnableCaching
@ServletComponentScan
@ComponentScan(basePackages={“xxx.xxx”,”xxx.xxx”})
@EnableScheduling
@EnableJpaRepositories(basePackages = {"com.lenovo.haha.repository.*", "com.lenovo.haha.dmeditor.repository.*", "com.lenovo.haha.*"})
@EntityScan(basePackages = "xxx.xxx.xxx")
```

#### 详解：

(1)@SpringBootApplication  
>包含了@ComponentScan、@Configuration和@EnableAutoConfiguration注解。
>>@ComponentScan：让spring Boot扫描到Configuration类并把它加入到程序上下文。
>
>>@Configuration：等同于spring的XML配置文件；使用Java代码可以检查类型安全。  
>
>>@EnableAutoConfiguration：启用 Spring 应用程序上下文的自动配置，
试图猜测和配置您可能需要的bean。自动配置类通常采用基于你的classpath 
和已经定义的 beans 对象进行应用。被 @EnableAutoConfiguration 注解的类所在的包
有特定的意义，并且作为默认配置使用。通常推荐将 @EnableAutoConfiguration 
配置在 root 包下，这样所有的子包、类都可以被查找到。

(2)@ImportResource  
>是用来加载xml配置文件
>>拓展：@Import注解是加载带有@Configuration的java类。

(3)@EnableAsync  
>Spring通过任务执行器（TaskExecutor）来实现多线程和并发编程。使用ThreadPoolTaskExecutor可实现一个基于线程池的TaskExecutor.在开发中实现异步任务，我们可以在配置类中添加@EnableAsync开始对异步任务的支持，并在相应的方法中使用@Async注解来声明一个异步任务。 
```
配置类：
@Configuration
@ComponentScan({"com.xingguo.logistics.service.aspect","com.xingguo.logistics.service.event"})
//开始异步支持
@EnableAsync
public class AopConfig implements AsyncConfigurer{
@Override
public Executor getAsyncExecutor() {
     //线程池
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.setCorePoolSize(5);
    taskExecutor.setMaxPoolSize(10);
    taskExecutor.setQueueCapacity(25);
    taskExecutor.initialize();
    return taskExecutor;
    }
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
         return null;
    }
}

执行类：
@Service
public class TestService2 {
    //声明异步任务
    @Async
    public void executeAsyncTask(Integer i){
         System.out.println("执行异步任务:"+i);
    }
    @Async
    public void executeAsyncTask2(Integer i){
         System.out.println("执行异步任务2:"+i);
    }
}
```

(4)@EnableCaching  
>spring boot提供了比较简单的缓存方案。只要使用 @EnableCaching即可完成简单的缓存功能。

(5)@ServletComponentScan
>在 SpringBootApplication 上使用@ServletComponentScan 注解后，Servlet、Filter、Listener 可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册，无需其他代码。

(6)@ComponentScan
>组件扫描。个人理解相当于<context:component-scan>，如果扫描到有@Component @Controller @Service等这些注解的类，则把这些类注册为bean。

(7)@EnableScheduling
>当开发者在AppConfig类中使用了本注解，并在某个Task类中使用了@Schedule注解，那么被@Schedule注解的标注的方法就可以在指定时间自动执行。
>https://blog.csdn.net/neulily2005/article/details/83750027

(8)@EnableJpaRepositories  
>用来扫描和发现指定包及其子包中的Repository定义  
>>①简单配置  
>>`@EnableJpaRepositories("com.spr.repository")`    
>
>>②支持多个package  
>>` @EnableJpaRepositories({"com.cshtong.sample.repository", "com.cshtong.tower.repository"})` 


(9)@EntityScan
>用来扫描和发现指定包及其子包中的Entity定义。  
>` @EntityScan(basePackages = {"com.department.entity","come.employee.entity"})` 


(10)@CommandLineRunner
>接口主要用于实现在应用初始化后，去执行一段代码块逻辑，
这段初始化代码在整个应用生命周期内只会执行一次。

①和@Component注解一起使用
````
@Component
public class ApplicationStartupRunner implements CommandLineRunner {
    protected final Log logger = LogFactory.getLog(getClass());
    @Override
    public void run(String... args) throws Exception {
        logger.info("ApplicationStartupRunner run method Started !!");
    }
}
````

②和@SpringBootApplication注解一起使用
````
@SpringBootApplication
public class SpringBootWebApplication extends SpringBootServletInitializer implements CommandLineRunner {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(SpringBootWebApplication.class);
    }
    public static void main(String[] args) throws Exception {
        SpringApplication.run(SpringBootWebApplication.class, args);
    }
    @Override
    public void run(String... args) throws Exception {
        logger.info("Application Started !!");
    }
}
````

③声明一个实现了CommandLineRunner接口的Bean
````
public class ApplicationStartupRunner implements CommandLineRunner {
    protected final Log logger = LogFactory.getLog(getClass());
    @Override
    public void run(String... args) throws Exception {
        logger.info("Application Started !!");
    }
}
````

### springMVC常用注解说明：

@RestController
>用于标注控制层组件(如struts中的action)，@ResponseBody和@Controller的合集。
>>@Controller：标注就控制层
>
>>@ResponseBody：表示该方法的返回结果直接写入HTTP response body中

@RequestMapping
>注解提供路由信息
>>①用在类上是访问这个控制器方法的总路径
>
>>②用在方法上是映射方法接口的URL
````
a：value，指定请求的地址 
b：method 请求方法类型 这个不写的话，自适应：get或者post
c：consumes 请求的提交内容类型 
d：produces 指定返回的内容类型 仅当request请求头中的(Accept)类型中包含该指定类型才返回
e: params 指定request中必须包含某些参数值 
f：headers 指定request中必须包含指定的header值
````

@GetMapping
>get请求

@PostMapping
>配置post请求

@RequestBody
>将请求参数封装到对象中接收

@PathVariable
>路径变量。如 :
````
RequestMapping(“user/get/mac/{macAddress}”) 
public String getByMacAddress(@PathVariable String macAddress){ 
//do something; 
} 
````
@RequestParam
>get请求时，从请求路径中获取对应参数

@EnablCaching
>是spring framework中的注解驱动的缓存管理功能。自spring版本3.1起加入了该注解。
如果你使用了这个注解，那么你就不需要在XML文件中配置cache manager了。


### spring常用注解说明：

@RestController:
>用于标注控制层组件(如struts中的action)，包含@Controller和@ResponseBody。

@Service:
>用于标注业务层组件。 

@Repository:
>用于标注数据访问组件，即DAO组件。

@Component：
>泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

@Autowired
>按类型注入.默认属性required= true;当不能确定 Spring 容器中一定拥有某个类的Bean 时，
 可以在需要自动注入该类 Bean 的地方可以使用 @Autowired(required = false)， 
 这等于告诉Spring：在找不到匹配Bean时也不抛出BeanCreationException 异常。
 @Autowired 和 @Qualifier 结合使用时，自动注入的策略就从 byType 转变byName 了。
 @Autowired可以对成员变量、方法以及构造函数进行注释，
 而 @Qualifier 的标注对象是成员变量、方法入参、构造函数入参。
 正是由于注释对象的不同，所以 Spring 不将 @Autowired 和 @Qualifier 统一成一个注释类。

````
@Autowired 
@Qualifier(value = “demoInfoService”) 
private DemoInfoService demoInfoService;
````

@Import
>注解是引入带有@Configuration的java类。  

@Resource(name="name",type="type")
>①没有括号内内容的话，默认byName。与@Autowired干类似的事。
>
>②@Autowired默认按照类型方式进行bean匹配

@RepositoryRestResourcepublic

@ConfigurationProperties
>Spring Boot将尝试校验外部的配置，默认使用JSR-303（如果在classpath路径中）。
>你可以轻松的为你的@ConfigurationProperties类添加JSR-303 javax.validation约束注解：
````
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
@NotNull
private InetAddress remoteAddress;
// ... getters and setters
}
````

@Configuration
>注解在类上，表示这是一个IOC容器，相当于spring的配置文件，java配置的方式。 
IOC容器的配置类一般与 @Bean 注解配合使用，
用 @Configuration 注解类等价与 XML 中配置 beans，
用@Bean 注解方法等价于 XML 中配置 bean。

@Bean
>相当于XML中的<bean></bean>,放在方法的上面，而不是类，意思是产生一个bean,并交给spring管理。 


@EnableConfigurationProperties  


@Profiles
>Spring Profiles提供了一种隔离应用程序配置的方式，并让这些配置只能在特定的环境下生效。
>
>任何@Component或@Configuration都能被@Profile标记，从而限制加载它的时机。

@Scope
>注解在类上，描述spring容器如何创建Bean实例。
>>①singleton： 表示在spring容器中的单例，通过spring容器获得该bean时总是返回唯一的实例  
>
>>②prototype：表示每次获得bean都会生成一个新的对象  
>
>>③request：表示在一次http请求内有效（只适用于web应用）  
>
>>④session：表示在一个用户会话内有效（只适用于web应用）  
>
>>⑤globalSession：表示在全局会话内有效（只适用于web应用）  

@Value
>注解在变量上，从配置文件中读取。  
>例如：@Value(value = “#{message}”)

@Transactional
>配置事务

### jpa常用注解说明：

@Entity
>标注实体

@Table
>标注实体对应的数据库表名

@Column
>通过@Column注解设置，包含的设置如下 
```` 
name：数据库表字段名 
unique：是否唯一 
nullable：是否可以为空 
Length:长度
inserttable：是否可以插入 
updateable：是否可以更新 
columnDefinition: 定义建表时创建此列的DDL 
secondaryTable: 从表名。如果此列不建在主表上（默认建在主表），该属性定义该列所在从表的名字。
````

@Id
>表示该属性为主键。

@GeneratedValue
>用于标注主键的生成策略，通过 strategy 属性指定。默认情况下，JPA 自动选择一个最适合底层数据库的主键生成策略：SqlServer 对应 identity，MySQL 对应 auto increment。 在 javax.persistence.GenerationType 中定义了以下几种可供选择的策略：
>
>>IDENTITY：采用数据库 ID自增长的方式来自增主键字段，Oracle 不支持这种方式；
>
>>AUTO： JPA自动选择合适的策略，是默认选项；
>
>>SEQUENCE：通过序列产生主键，通过 @SequenceGenerator 注解指定序列名，MySql 不支持这种方式
>
>>TABLE：通过表产生主键，框架借由表模拟序列产生主键，使用该策略可以使应用更易于数据库移植。

@SequenceGeneretor(name = "repair_seq", sequenceName = "seq_repair", allocationSize = 1)
>name为sequence的名称，以便使用，sequenceName为数据库的sequence名称，
两个名称可以一致。

@Temporal
>设置为时间类型  

@Transient
>表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性。
如果一个属性并非数据库表的字段映射,就务必将其标示为@Transient,
否则,ORM框架默认其注解为@Basic。@Basic(fetch=FetchType.LAZY)：
标记可以指定实体属性的加载方式

@Query 
>自定义查询语句 JPQL

@Modifying
>如果是增，改，删加上此注解  
>>方法的返回值应该是int，表示更新语句所影响的行数。  
>
>>在调用的地方必须加事务，没有事务不能正常执行。  

@JsonIgnore
>作用是json序列化时将Java bean中的一些属性忽略掉,序列化和反序列化都受影响。


@JoinColumn
>一对一：本表中指向另一个表的外键。  
>一对多：另一个表指向本表的外键。

@OneToOne、@OneToMany、@ManyToOne
>对应hibernate配置文件中的一对一，一对多，多对一。

@MappedSuperClass
>@MappedSuperclass 注解使用在父类上面，是用来标识父类的
>
>@MappedSuperclass 标识的类表示其不能映射到数据库表，因为其不是一个完整的实体类，但是它所拥有的属性能够映射在其子类对用的数据库表中
>
>@MappedSuperclass 标识的类不能再有@Entity或@Table注解

@NoRepositoryBean
>一般用作父类的repository，有这个注解，spring不会去实例化该repository。


### 其他注解说明：

全局异常处理：  
@ControllerAdvice：
>包含@Component。可以被扫描到。
统一处理异常。

@ExceptionHandler（Exception.class）：
>用在方法上面表示遇到这个异常就执行以下方法。

@MapperScan("cn.qiu.mapper")
>mybatis框架中的dao扫描

@Data（Lombok）
>注解在类上；提供类所有属性的 getting 和 setting 方法，此外还提供了equals、canEqual、hashCode、toString 方法

@Setter
>注解在属性上；为属性提供 setting 方法

@Getter
>注解在属性上；为属性提供 getting 方法

@Log4j2 
>注解在类上；为类提供一个 属性名为log 的 log4j 日志对象，和@Log4j注解类似

@NoArgsConstructor
>注解在类上；为类提供一个无参的构造方法

@AllArgsConstructor
>注解在类上；为类提供一个全参的构造方法

@EqualsAndHashCode
>默认情况下，会使用所有非瞬态(non-transient)和非静态(non-static)字段来生成equals和hascode方法，也可以指定具体使用哪些属性。

@toString
>生成toString方法，默认情况下，会输出类名、所有属性，属性会按照顺序输出，以逗号分割。

@NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor
>无参构造器、部分参数构造器、全参构造器，当我们需要重载多个构造器的时候，只能自己手写了

@NonNull
>注解在属性上，如果注解了，就必须不能为Null

@val
>注解在属性上，如果注解了，就是设置为final类型，可查看源码的注释知道

@PreUpdate
````
1.用于为相应的生命周期事件指定回调方法。
2.该注释可以应用于实体类，映射超类或回调监听器类的方法。
3.用于setter 如果要每次更新实体时更新实体的属性，可以使用@PreUpdate注释。
4.使用该注释，您不必在每次更新用户实体时显式更新相应的属性。
5.preUpdate不允许您更改您的实体。 您只能使用传递给事件的计算的更改集来修改原始字段值。
````
@Prepersist
````
1.查看@PrePersist注释，帮助您在持久化之前自动填充实体属性。
2.可以用来在使用jpa的时记录一些业务无关的字段，比如最后更新时间等等。生命周期方法注解（delete没有生命周期事件）
3.@PrePersist save之前被调用，它可以返回一个DBObject代替一个空的 @PostPersist save到datastore之后被调用 
4.@PostLoad 在Entity被映射之后被调用 @EntityListeners 指定外部生命周期事件实现类 
````