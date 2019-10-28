# springboot去掉内置tomcat使用外部tomcat容器

## 第一步，pom.xml中添加插件
```
 <build>
        <plugins>
            <!--  ............war包解开，start............  -->
            <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
            <fork>true</fork>
            </configuration>
            </plugin>
            <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            </plugin>
            <!--  ............war包解开，end............  -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>src/main/java</directory>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <!--<excludes>-->
                <!--<exclude>**/*.*</exclude>-->
                <!--</excludes>-->
            </resource>
        </resources>
        <finalName>editor</finalName>
    </build>
```


## 第二步，pom.xml中去除对默认日志的依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <!-- 去除对默认日志的依赖 -->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j</artifactId>
    <version>1.3.8.RELEASE</version>
</dependency>
```

## 第三步，pom.xml中添加`<packaging>war</packaging>`
```
<groupId>com.lenovo.dm</groupId>
<artifactId>editor</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>editor</name>
<packaging>war</packaging>
<description>DMEditor all pro</description>
```

## 第四步，启动类同目录下添加SpringBootWebApplication类
```
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

public class SpringBootWebApplication extends SpringBootServletInitializer {

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        // 注意这里要指向原先用main方法执行的Application启动类
        return builder.sources(EditorApplication.class);
    }
}
```
## 第五步，运行打包部署，扔到tomcat中的webapp包下。
