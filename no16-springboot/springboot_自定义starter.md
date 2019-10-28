# 自定义starter

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