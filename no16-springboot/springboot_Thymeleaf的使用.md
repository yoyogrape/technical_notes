#  Thymeleaf 的使用

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

##### 2)"/**" 访问当前项目的任何资源，都去（静态资源的文件夹）找映射

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
