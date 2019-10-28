# springboot学习笔记—（上）
## 第一章 springboot简介
### 第一节 springboot的部署方式
1）win 直接打包成jar然后运行命令
````
java -jar 包名
````

2）linux 解压扔在tomcat的webapps目录下,然后重启tomcat


### 第二节 导入后写配置文件的时候会有提示
```
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```


### 第三节 将yml中信息注入到类中

```
1）实体类
@Data
@Component //只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
@ConfigurationProperties(prefix = "person")
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
}

2）yml
person:
  lastName: hello
  age: 18
  boss: false
  birth: 2017/12/12
  maps: {k1: v1,k2: 12}
  lists:
    - lisi
    - zhaoliu
  dog:
    name: 小狗
    age: 12

3）pom添加依赖
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>

4）测试类
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootPrimaryApplicationTests {
    @Autowired
    Person person;
    @Test
    public void contextLoads() {
        System.out.println(person);
    }
}
```

### @SpringBootApplication 
说明：来标注一个主程序类，说明这是一个Spring Boot应用


### @Configuration:配置类上来标注这个注解
配置类 ----- 配置文件；配置类也是容器中的一个组件；@Component


### @EnableAutoConfiguration：开启自动配置功能

### @AutoConfigurationPackage：自动配置包


### @Import(AutoConfigurationPackages.Registrar.class)：
Spring的底层注解@Import，给容器中导入一个组件；导入的组件由AutoConfigurationPackages.Registrar.class；  
将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；



### @Value获取值和@ConfigurationProperties获取值比较

|   |@ConfigurationProperties|@Value|
|:--------|:---:|---| 
| 功能         | 批量注入配置文件中的属性             | 一个个指定  |
| 松散绑定（松散语法） | 支持                       | 不支持    |
| SpEL       | 不支持                      | 支持     |
| JSR303数据校验 | 支持                       | 不支持    |
| 复杂类型封装     | 支持                       | 不支持    |

配置文件yml还是properties他们都能获取到值；

如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；
如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConfigurationProperties

### @PropertySource：加载指定的配置文件
```
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
//@Validated
public class Person {
```

### @ImportResource：导入Spring的配置文件，让配置文件里面的内容生效；

```
//加在主配置类上，导入Spring的配置文件让其生效
@ImportResource(locations = {"classpath:beans.xml"})
public class SpringbootPrimaryApplication {
```

## 第二章 日志记录
springboot日志框架

### 第一节 SLF4j使用
1、如何在系统中使用SLF4j https://www.slf4j.org
以后开发的时候，日志记录方法的调用，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法；
给系统里面导入slf4j的jar和 logback的实现jar
```
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class HelloWorld {
    public static void main(String[] args) {
        Logger logger = LoggerFactory.getLogger(HelloWorld.class);
        logger.info("Hello World");
    }  
}
```

如何让系统中所有的日志都统一到slf4j；  
1、将系统中其他日志框架先排除出去；  
2、用中间包来替换原有的日志框架；  
3、我们导入slf4j其他的实现  

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring‐core</artifactId>
    <exclusions>
        <exclusion>
            <groupId>commons‐logging</groupId>
            <artifactId>commons‐logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

```
SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要
把这个框架依赖的日志框架排除掉即可；

```
//记录器
Logger logger = LoggerFactory.getLogger(getClass());
@Test
public void contextLoads() {
    //System.out.println();
    //日志的级别；
    //由低到高 trace<debug<info<warn<error
    //可以调整输出的日志级别；日志就只会在这个级别以以后的高级别生效
    logger.trace("这是trace日志...");
    logger.debug("这是debug日志...");
    //SpringBoot默认给我们使用的是info级别的，没有指定级别的就用SpringBoot默认规定的级别；root
    级别
    logger.info("这是info日志...");
    logger.warn("这是warn日志...");
    logger.error("这是error日志...");
}

```

````
日志输出格式：
%d表示日期时间，
%thread表示线程名，
%‐5level：级别从左显示5个字符宽度
%logger{50} 表示logger名字最长50个字符，否则按照句点分割。
%msg：日志消息，
%n是换行符
‐‐>
%d{yyyy‐MM‐dd HH:mm:ss.SSS} [%thread] %‐5level %logger{50} ‐ %msg%n
````

### 第二节 SpringBoot修改日志的默认配置
```
logging.level.com.atguigu=trace
#logging.path=
# 不指定路径在当前项目下生成springboot.log日志
# 可以指定完整的路径；
#logging.file=G:/springboot.log
# 在当前磁盘的根路径下创建spring文件夹和里面的log文件夹；使用 spring.log 作为默认文件
logging.path=/spring/log
# 在控制台输出的日志的格式
logging.pattern.console=%d{yyyy‐MM‐dd} [%thread] %‐5level %logger{50} ‐ %msg%n
# 指定文件中日志输出的格式
logging.pattern.file=%d{yyyy‐MM‐dd} === [%thread] === %‐5level === %logger{50} ==== %msg%n
```

## 第三章 web开发
###  第一节 webjars的使用  
##### 1)所有 /webjars/** ，都去 classpath:/META-INF/resources/webjars/ 找资源；
以jar包的方式引入资源  
官方地址：https://www.webjars.org/

启动项目以后访问以下路径可以访问项目中的静态资源。
http://localhost:8080/webjars/jquery/3.3.1-2/jquery.js/



```
<!‐‐引入jquery‐webjar‐‐>在访问的时候只需要写webjars下面资源的名称即可
<dependency>
<groupId>org.webjars</groupId>
<artifactId>jquery</artifactId>
<version>3.3.1</version>
</dependency>
```

##### 2)`/**` 访问当前项目的任何资源，都去（静态资源的文件夹）找映射

```
"classpath:/META‐INF/resources/",
"classpath:/resources/",
"classpath:/static/",
"classpath:/public/"
"/"：当前项目的根路径
```
##### 3）欢迎页； 静态资源文件夹下的所有index.html页面；被"/**"映射；
localhost:8080/ 找index页面

##### 4）所有的 **/favicon.ico 都是在静态资源文件下找；


### 第二节 Thymeleaf使用

1)导入thymeleaf的名称空间
```
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

2)语法规则
```
1 Fragment inclusion th:insert 片段包含
th:replace
2 Fragment iteration th:each  遍历
3 Conditional evaluation th:if  判断
th:unless
th:switch
th:case
4 Local variable definition th:object  声明变量
th:with
5 General attribute modification th:attr  修改属性
th:attrprepend
th:attrappend
6 Specific attribute modification th:value  修改指定属性任意值
th:href
th:src
...
7 Text (tag body modification) th:text 修改标签内容 （转义特殊字符）
th:utext  修改标签内容 （不转义特殊字符）
8 Fragment specification th:fragment  声明片段
9 Fragment removal th:remove
```

3)五种表达式语法应用示例


${...}  
①获取对象属性、调用方法  
②使用基本的内置对象
```
#ctx : the context object.
#vars: the context variables.
1 2 3 4 5 6 7 8 9
10
11
12
1 2 3 4 5#vars: the context variables.
#locale : the context locale.
#request : (only in Web Contexts) the HttpServletRequest object.
#response : (only in Web Contexts) the HttpServletResponse object.
#session : (only in Web Contexts) the HttpSession object.
#servletContext : (only in Web Contexts) the ServletContext object.
```
③使用内置的工具对象
```
#execInfo : information about the template being processed.
#messages : methods for obtaining externalized messages inside variables expressions, in the
same way as they would be obtained using #{…} syntax.
#uris : methods for escaping parts of URLs/URIs
#conversions : methods for executing the configured conversion service (if any).
#dates : methods for java.util.Date objects: formatting, component extraction, etc.
#calendars : analogous to #dates , but for java.util.Calendar objects.
#numbers : methods for formatting numeric objects.
#strings : methods for String objects: contains, startsWith, prepending/appending, etc.
#objects : methods for objects in general.
#bools : methods for boolean evaluation.
#arrays : methods for arrays.
#lists : methods for lists.
#sets : methods for sets.
#maps : methods for maps.
#aggregates : methods for creating aggregates on arrays or collections.
#ids : methods for dealing with id attributes that might be repeated (for example, as a
result of an iteration).
```

*{...}  
选择表达式：和${}在功能上是一样；  
①和上面的${}功能一样  
②补充功能：配合th:Object,可以直接取对象的属性

```#{...}```  
获取国际化内容

@{...}定义URL  
```
示例一：
@{/order/process(execId=${execId},execType='FAST')}
示例二：
th:href="@{/webjars/bootstrap/4.3.1/css/bootstrap.css}"
注意：地址前面需要‘/’
说明：页面中路径要用th：进行获取，在增加根路径server.context-path:/songjn,的时候静态资源前会增加该根路径
避免访问不到静态资源
```
~{...}  
片段引用表达式  


说明：在进行th:each遍历的时候有一个高级特性
```
[[${对象.属性}]]  会转义特殊字符
[（${对象.属性}）] 不会转义特殊字符
``` 


### 第三节 页面的国际化
1）首先，编写国际化properties文件
```
resources
  -i18n
    -login.properties
    -login_en_US.properties
    -login_zh_CN.properties

```
2）配置文件中指定国际化文件路径
```
spring:
  messages:
    basename: i18n.login
```
3）修改页面，请求时URL需要加国际化参数
```
<a class="btn btn-sm" th:href="@{/login.html(len='zh_CN')}">中文</a>
<a class="btn btn-sm" th:href="@{/login.html(len='en_US')}">English</a>
```
4）添加解析器
```
public class MyLocalResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
        System.out.println("---MyLocalResolver---resolveLocale()---");
        String len= httpServletRequest.getParameter("len");
        Locale locale = Locale.getDefault();
        if(!StringUtils.isEmpty(len)){
            String[] split = len.split("_");
            locale = new Locale(split[0], split[0]);
        }
        return locale;
    }
```
5）在前端适配器中启动解析器
```
@Configuration
public class MyMvcAdapter extends WebMvcConfigurerAdapter {
    @Bean
    public LocaleResolver myResolver() {
        System.out.println("---MyMvcAdapter---myResolver()---");
        return new MyLocalResolver();
    }
```



### 第四节 登录实现
开发期间模板引擎修改页面后需要实时生效  
1）禁用模板引擎的缓存；
```
spring:
  thymeleaf:
    cache: false
```
2）页面修改后按ctrl+F9:重新编译；

3）登录错误消息的显示
```
<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
```

### 第五节 解决表单重复提交问题（进行页面的重定向）
1）在控制器中使用redirect进行页面的重定向

```
 return "redirect:/main";
 
     @GetMapping("/main")
     public String home() {
         System.out.println("---LoginController---home()---");
         return "home";
     }
```

### 第六节 拦截器进行页面登录检查

1）如果登录成功，将用户名保存到session中
```
session.setAttribute("loginUser", userName);
```
2）定义自己的拦截器，每次检查session中有没有用户信息
```
public class LoginHandlerInterceptor implements HandlerInterceptor {
    //目标方法执行之前
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        Object loginUser = request.getSession().getAttribute("loginUser");
        if (loginUser == null) {
            //该用户没有登录，返回登录页面
            request.setAttribute("msg", "该用户没有权限，请先登录");
            request.getRequestDispatcher("/").forward(request, response);
            return false;
        } else {
            //已经登录,放行
            return true;
        }
    }
```
3）在前端适配器MyMvcAdapter中，执行自己定义的拦截器。并设置要拦截的资源路径
```
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //静态资源；  *.css , *.js
        //SpringBoot已经做好了静态资源映射
        //  "/":排除登录页面
        //  "/login"：排除点击登录的请求
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                .excludePathPatterns("/", "/login", "/index");
        super.addInterceptors(registry);
    }
```

###  第七节 CRUD员工列表
实验要求：
1）、RestfulCRUD：CRUD满足Rest风格；
URI： /资源名称/资源标识 HTTP请求方式区分对资源CRUD操作


|  |普通CRUD（uri来区分操作）|RestfulCRUD|
|:--------|:---|:---| 
|查询|getEmp|emp---GET|
|添加|addEmp?xxx|emp---POST|
|修改|updateEmp?id=xxx&xxx=xx|emp/{id}---PUT|
|删除|deleteEmp?id=1 emp/{id}| emp/{id}---DELETE|


### 第八节 thymleaf公共片段的抽取
```
1、抽取公共片段
<div th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</div>
2、引入公共片段
<div th:insert="~{footer :: copy}"></div>
~{templatename::selector}：模板名::选择器
~{templatename::fragmentname}:模板名::片段名
3、默认效果：
insert的公共片段在div标签中
如果使用th:insert等属性进行引入，可以不用写~{}：
行内写法可以加上：[[~{}]];[(~{})]；
```
三种引入公共片段的th属性：  
th:insert：将公共片段整个插入到声明引入的元素中  
th:replace：将声明引入的元素替换为公共片段  
th:include：将被引入的片段的内容包含进这个标签中  
```
<footer th:fragment="copy">
&copy; 2011 The Good Thymes Virtual Grocery
</footer>
引入方式
<div th:insert="footer :: copy"></div>
1 2 3 4 5 6 7 8 9
10
11
12
13
14
1 2 3 4 5 6引入片段的时候传入参数：
6）、CRUD-员工添加
添加页面
<div th:replace="footer :: copy"></div>
<div th:include="footer :: copy"></div>
效果
<div>
<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>
</div>
<footer>
&copy; 2011 The Good Thymes Virtual Grocery
</footer>
<div>
&copy; 2011 The Good Thymes Virtual Grocery
</div>
```
### 第九节 点击高亮的实现
1）引入公共片段的时候传入参数activeUri='emps'
```
<div th:replace="~{commons/bar::sidebar(activeUri='emps')}"></div>
```
2）在bar.html页面进行参数判断，然后定义属性设置
```
<a class="nav-link" th:class="${ activeUri =='emps'?'nav-link active':'nav-link'}"
```

### 第十节 错误处理机制(如何添加错误页面)

1）在templates目录下新建文件夹/error,将4xx.html，5xx.html放到里面。

2）页面中能获取的信息：
```
页面能获取的信息；
timestamp：时间戳
status：状态
error：错误提
exception：异常对象
message：异常消息
errors：JSR303数据校验的错误都在
```
3）页面中获取信息
```
<h1>status:[[${status}]]</h1>
<h2>timestamp:[[${timestamp}]]</h2>
```

## 第四章 如何定制和修改Servlet容器的相关配置

### 第一节 修改和server有关的配置（ServerProperties【也是EmbeddedServletContainerCustomizer】）；

```
server.port=8081
server.context‐path=/crud
server.tomcat.uri‐encoding=UTF‐8
//通用的Servlet容器设置
server.xxx
//Tomcat的设置
server.tomcat.xxx

```
### 第二节 注册Servlet三大组件【Servlet、Filter、Listene】


### 使用外置的Servlet容器
1）、必须创建一个war项目；（利用idea创建好目录结构）  
2）、将嵌入式的Tomcat指定为provided；
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐starter‐tomcat</artifactId>
    <scope>provided</scope>
</dependency>
```
3）、必须编写一个SpringBootServletInitializer的子类，并调用configure方法

```
public class ServletInitializer extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        //传入SpringBoot应用的主程序
        return application.sources(SpringBoot04WebJspApplication.class);
    }
}
```

## 第五章 docker

### 第一节 核心概念
```
docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；
docker客户端(Client)：连接docker主机进行操作；
docker仓库(Registry)：用来保存各种打包好的软件镜像；
docker镜像(Images)：软件打包好的镜像；放在docker仓库中；
docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用
```
### 第二节 安装docker
1）安装虚拟机  
2）配置好虚拟机的网卡  
3）查看虚拟机的ip，命令 
```
ip addr
``` 
4）查看虚拟机内核版本，必须是3.0以上版本。
```
uname -r
```
5）安装docker命令
```
yum install docker
```
6）输入y确认
7）启动docker
```
systemctl start docker

docker -v
```
8）开机启动docker
```
systemctl enable docker
```
9）停止docker
```
systemctl stop docker
```

### 第三节 docker 常用命令
docker仓库地址，可以查看库里面有没有想要的镜像。
```
https://hub.docker.com/
```
1）查询仓库中是否有该镜像
```
docker search mysql
docker search redis

查询说明：
OFFICIAL：是否是官方的
AUTOMATED：是否是自动构建的
```

2）下载让软件 标签可以指定版本
```
命令：
docker pull 镜像名 [标签]

[root@localhost docker]# docker pull mysql
Using default tag: latest //下载最新的mysql
```
3）查看所有本地镜像
```
docker images ///查看所有本地镜像
```
4）删除指定的本地镜像
```
docker rmi 镜像ID
```

### 第四节 解决docker启动问题
1）启动遇见报错信息
```
Job for docker.service failed because the control process exited with error code. 
See "systemctl status docker.service" and "journalctl -xe" for details.
```
2）查看docker服务状态信息
```
命令：
systemctl status docker.service

错误信息中有这样一句话：
Error starting daemon: SELinux is not supported wi...se)
```

3）网上查找原因：：：  
docker配置文件没有关闭selinux
```
vi /etc/sysconfig/docker
将--selinux-enabled参数改为false
OPTIONS='--selinux-enabled=false --log-driver=journald --signature-verification=false'
```
4）重启docker，问题解决
```
systemctl start docker
```

### 第五节 解决docker因为网络原因不能下载问题
1）下载bind-utils工具包  
bind是linux系统下的一个DNS服务程序.bind-utils是bind软件提供的一组DNS工具包,里面有一些DNS相关的工具.
主要有:dig,host,nslookup,nsupdate.使用这些工具可以进行域名解析和DNS调试工作.
```
yum install bind-utils
```
2）查找 registry-1.docker.io 找到可用IP
```
命令：
dig @114.114.114.114 registry-1.docker.io

[root@archlinux ~]# dig @114.114.114.114 registry-1.docker.io 
; <<>> DiG 9.12.0 <<>> @114.114.114.114 registry-1.docker.io 
; (1 server found) 
;; global options: +cmd 
;; Got answer: 
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37534 
;; flags: qr rd ra; QUERY: 1, ANSWER: 8, AUTHORITY: 0, ADDITIONAL: 1 
;; OPT PSEUDOSECTION: 
; EDNS: version: 0, flags:; udp: 4096 
; COOKIE: 88f87a15f4ca45dc (echoed) 
;; QUESTION SECTION: 
;registry-1.docker.io. IN A 
;; ANSWER SECTION: 
registry-1.docker.io. 30 IN A 34.200.28.105 
registry-1.docker.io. 30 IN A 52.22.181.254 
registry-1.docker.io. 30 IN A 35.169.231.249 
registry-1.docker.io. 30 IN A 52.5.185.86 
registry-1.docker.io. 30 IN A 54.164.230.151 
registry-1.docker.io. 30 IN A 34.200.90.16 
registry-1.docker.io. 30 IN A 52.20.146.203 
registry-1.docker.io. 30 IN A 52.206.156.207 
;; Query time: 116 msec 
;; SERVER: 114.114.114.114#53(114.114.114.114) 
;; WHEN: 四 3月 08 09:27:12 CST 2018 
;; MSG SIZE rcvd: 189
```
3）尝试修改/etc/hosts强制docker.io相关的域名解析到其它可用IP
```
vi /etc/hosts

52.5.185.86 registry-1.docker.io
```
4）保存之后重试
```
systemctl start docker
```

### 第六节 运行镜像步骤
```
1、搜索镜像
[root@localhost ~]# docker search tomcat
2、拉取镜像
[root@localhost ~]# docker pull tomcat
3、根据镜像启动容器
docker run ‐‐name mytomcat ‐d tomcat:latest
    ‐‐name：给容器取的名字 
    ‐d：后台运行
4、docker ps
查看运行中的容器
5、 停止运行中的容器
docker stop 容器的id
6、查看所有的容器
docker ps ‐a
7、启动容器
docker start 容器id
8、删除一个容器
docker rm 容器id

9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d --name=DM_tomcat -p 8888:8080 tomcat:latest
‐d：后台运行
 --name=DM_tomcat：指定容器名称
‐p: 将主机的端口映射到容器的一个端口 主机端口:容器内部的端口
tomcat:镜像的名称    

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container‐name/container‐id
更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档
12、可以用一个镜像启动多个镜像
```
### 第七节 mysql的安装
1）下载mysql
```
docker pull mys
```
2）先讲一种错误的启动方式，因为需要设置密码
```
docker run ‐‐name=mysql01 ‐d mysql

因为需要对数据库进行密码的设置，所以需要设置参数启动
[root@localhost ~]# docker run ‐p 3306:3306 ‐‐name mysql02 ‐e MYSQL_ROOT_PASSWORD=123456 ‐d mysql

```
3）正确的启动方式
```
[root@localhost ~]# sudo docker run --name mysql_0325 -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -d mysql
```
4）几个其他的高级操作
```
docker run ‐‐name mysql03 ‐v /conf/mysql:/etc/mysql/conf.d ‐e MYSQL_ROOT_PASSWORD=my‐secret‐pw
‐d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）
docker run ‐‐name some‐mysql ‐e MYSQL_ROOT_PASSWORD=my‐secret‐pw ‐d mysql:tag ‐‐character‐set‐
server=utf8mb4 ‐‐collation‐server=utf8mb4_unicode_ci
指定mysql的一些配置参数

```
### 第八节 docker中国
docker在下载镜像的时候连接的是国外的服务器，下载很慢，可以连docker中国，进行镜像加速。  
docker中国地址：`https://www.docker-cn.com/`  
下载命令：

```
docker pull registry.docker-cn.com/library/镜像名称
```

## 第六章 数据库交互


### 第一节 集成 druid

1） 在配置文件中配置数据库连接信息
```
yml:

spring:
  profiles:
    active: dev
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://${dbs.database.host}:${dbs.database.port}/${dbs.database.databasename}?characterEncoding=UTF-8
    username: ${dbs.database.username}
    password: ${dbs.database.password}
    # 初始化大小，最小，最大
    initialSize: 5
    minIdle: 5
    maxActive: 500
    # 配置获取连接等待超时的时间
    maxWait: 60000
    # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
    timeBetweenEvictionRunsMillis: 60000
    # 配置一个连接在池中最小生存的时间，单位是毫秒
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    maxPoolPreparedStatementPerConnectionSize: 20
    # 通过connectProperties属性来打开mergeSql功能；慢SQL记录
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
    # 合并多个DruidDataSource的监控数据
    #useGlobalDataSourceStat=true
    
    
    properties:
    
    profile=dev
    #======================== 开发自测环境 =======================================
    dbs.database.host=10.110.147.194
    dbs.database.port=3306
    dbs.database.username=
    dbs.database.password=
    dbs.database.databasename=
```

2）druid配置文件，springboot自己读取配置文件

```
import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.support.http.StatViewServlet;
import com.alibaba.druid.support.http.WebStatFilter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;
import java.util.Arrays;
import java.util.HashMap;
import java.util.Map;

@Configuration
public class DruidConfig {

    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druid() {
        return new DruidDataSource();
    }

    //配置Druid的监控
    //1、配置一个管理后台的Servlet
    @Bean
    public ServletRegistrationBean statViewServlet() {
        ServletRegistrationBean bean = new ServletRegistrationBean(new StatViewServlet(), "/druid/*");
        Map<String, String> initParams = new HashMap<>();

        initParams.put("loginUsername", "admin");
        initParams.put("loginPassword", "123456");
        initParams.put("allow", "");//默认就是允许所有访问
        initParams.put("deny", "192.168.15.21");

        bean.setInitParameters(initParams);
        return bean;
    }


    //2、配置一个web监控的filter
    @Bean
    public FilterRegistrationBean webStatFilter() {
        FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new WebStatFilter());

        Map<String, String> initParams = new HashMap<>();
        initParams.put("exclusions", "*.js,*.css,/druid/*");

        bean.setInitParameters(initParams);

        bean.setUrlPatterns(Arrays.asList("/*"));

        return bean;
    }
}

```
3）测试类
```
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import javax.sql.DataSource;
import java.sql.Connection;
import java.sql.SQLException;

@RunWith(SpringRunner.class)
@SpringBootTest
public class ConnectDbTestApplicationTests {
	//
	@Autowired
	DataSource dataSource;

	@Test
	public void contextLoads() throws SQLException {
		System.out.println("======连接测试开始======");
		System.out.println(dataSource.getClass());
		Connection connection = dataSource.getConnection();
		System.out.println(connection);
		connection.close();
		System.out.println("======连接测试结束======");
	}
}
```

### 第二节 注解集成mybatis

1）创建两个测试实体

```
@Data
public class Department {
    private Integer id;
    private String dname;
}


@Data
public class Employee {
    private Integer id;
    private String lastName;
    private String email;
    private Integer gender;
    private Integer d_id;
}

```

2）创建对应的mapper
```
@Mapper
public interface DepartmentMapper {

    @Select("select * from department where id=#{did}")
    Department getDeptById(Integer did);

    @Delete("delete from department where id=#{did}")
    Integer delDeptById(Integer did);

    @Insert("insert department(dname) value(#{dname})")
    Integer insertDept(Department department);

    @Update("update department set dname=#{dname} where id=#{id}")
    Integer updateDept(Department department);

}

```
3）测试
```
@RestController
public class DeptController {
    @Autowired
    private DepartmentMapper dao;
    @RequestMapping("/dept/getOne")
    public void getDept(){
        System.out.println("---DeptController---getDept()---");
        Department dept = dao.getDeptById(1);
        System.out.println(dept.toString());
    }
}
```
4）说明  
可以去掉mapper上面的注解，在启动类上进行全局扫描
```
@MapperScan(value = "com.songjn.springboot_mybatis.mapper")
```

### 第三节 配置文件集成mybatis
1）创建mapper文件
```
//@Mapper或者@MapperScan将接口扫描装配到容器中
public interface EmployeeMapper {

    Employee getEmpById(Integer id);

    void insertEmp(Employee employee);
}

```
2）添加全局扫描
```
@MapperScan(value = "com.songjn.springboot_mybatis.mapper")
@SpringBootApplication
public class SpringbootMybatisApplication {
	public static void main(String[] args) {
		SpringApplication.run(SpringbootMybatisApplication.class, args);
	}
}
```
3）增加mybatis配置文件
```
-resource
    -mapper
        EmployeeMapper.xml
    mybatis-config.xml 
    
①mybatis-config.xml如下：

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>

②EmployeeMapper.xml如下：

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.songjn.springboot_mybatis.mapper.EmployeeMapper">
   <!--    public Employee getEmpById(Integer id);

    public void insertEmp(Employee employee);-->
    <select id="getEmpById" resultType="com.songjn.springboot_mybatis.entity.Employee">
        SELECT * FROM employee WHERE id=#{id}
    </select>

    <insert id="insertEmp">
        INSERT INTO employee(lastName,email,gender,d_id) VALUES (#{lastName},#{email},#{gender},#{dId})
    </insert>
</mapper>
```
4）yml中配置xml文件的位置信息
```
mybatis:
  config-location: classpath:mybatis/mybatis-config.xml
  mapper‐locations: classpath:mybatis/mapper/*.xml

```
## 第七章 自定义starter

1）首先创建一个空工程  (工程里面什么都没有)  
名称：springboot-songjn-starter

2）创建一个model，作为启动器  
里面只有一个pom文件。依赖自动配置的初始化器。  

GroupId：com.songjn.starter  
ArtifactId：songjn-springboot-starter

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.songjn.starter</groupId>
    <artifactId>songjn-springboot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <!--引入自动配置模块-->
            <groupId>com.songjn.starter</groupId>
            <artifactId>songjn-springboot-starter-auto-autoconfigurer</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

3）创建另一个model，springboot的初始化器，做自动配置，里面写运行的方法代码  
 
Group：com.songjn.starter  
artifactId：songjn-springboot-starter-auto-autoconfigurer

```
pom文件：只依赖spring-boot-starter一个。

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.songjn.starter</groupId>
    <artifactId>songjn-springboot-starter-auto-autoconfigurer</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>songjn-springboot-starter-auto-autoconfigurer</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!--引入spring-boot-starter。所有start都需要引入的配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
    </dependencies>
</project>

```
4）在自动配置模块中删除启动类。只留下空目录，然后进行业务代码的编写
业务代码分为四步，  

第一步，HelloService业务代码  
```
package com.songjn.starter;
/**
 * @author songjn
 * @create 2019-03-20 23:12
 * @desc  测试service
 **/
public class HelloService {

    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }

    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }

    public  String sayHello(String say){

        System.out.println("---HelloService---sayHello()---");

        return helloProperties.getPrefix()+"---你想说的是"+say+"---"+helloProperties.getSuffix();
    }
}

```

第二步，properties类，绑定配置文件中的属性。
```
package com.songjn.starter;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

/**
 * @author songjn
 * @create 2019-03-20 23:19
 * @desc  绑定配置文件中的属性
 **/
@ConfigurationProperties(prefix = "songjn.hello")
public class HelloProperties {
    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}

```
第三步，自动配置类，将业务类注入到容器中

```
package com.songjn.starter;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author songjn
 * @create 2019-03-20 23:25
 * @desc  将业务类注入到容器中
 **/
@Configuration
@ConditionalOnWebApplication//web应用才生效
@EnableConfigurationProperties(HelloProperties.class)
public class HelloServiceAutoConfiguration {
    @Autowired
    HelloProperties helloProperties;

    @Bean
    public HelloService helloService() {
        HelloService helloService = new HelloService();
        helloService.setHelloProperties(helloProperties);
        return helloService;
    }
}
```
第四步，增加配置文件spring.factories，将配置文件注入到整个容器中
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.songjn.starter.HelloServiceAutoConfiguration
```

5）将两个模块先后顺序发布到版本库中。

6）其他项目引用的方法，直接添加启动器的坐标就可以进行业务方法的使用。
```
pom引用:
<dependency>
    <groupId>com.songjn.starter</groupId>
    <artifactId>songjn-springboot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

```
测试类：

package com.songjn.springboottestsongjnstarter.controller;

import com.songjn.starter.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author songjn
 * @create 2019-03-20 23:44
 * @desc 测试自己创建的starter
 **/
@RestController
public class TestController {
    @Autowired
    HelloService helloService;

    @RequestMapping("/test")
    public String testSay() {
        String say = helloService.sayHello("中国美");
        System.out.println(say);
        return "";
    }
}

```


```
配置文件properties中

songjn.hello.prefix=1111
songjn.hello.suffix=2222
```

```
运行结果：
---HelloService---sayHello()---
1111---你想说的是中国美---2222

```