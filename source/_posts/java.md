---
title: java 基础整理
date: 2018-06-12 10:51:54
tags: java spring
---

# java-kylin


## java

### 与.Net不一样的

#### 差异

+ 方法规则采用:lowerCamelCase,类命名规则:UpperCamelCase
+ 类继承 extend 接口实现:implement
+ 实现接口的方式@Override 特性
+ 虚方法virtual Java 没有对应的关键字:**在运行时，会遗忘子类对象中与父类对象中不同的方法。也会覆盖与父类中相同的方法--重写**
+ 抽象方法 abstract
+ Java枚举 中可以定义方法/可以继承接口，java中的枚举其实就是一种特殊的类
+ 接口不要用I开头如 IUser 应该为 UserImpl-->User
+ .Net 事件/委托   事件的定义 在.net中会生成一个内部类/接口，JAVA内部类方式模拟多继承
+ .NET中的特性Attribute ->JAVA 中的注解 @interface
+ JVM-CLR:(JVM可以配置更多参数:初始化内存/最大内存/GC策略等)
+ (并发包)System.Collections.Concurrent->java.util.concurrent
+ 泛型约束 <? extends Object> / <? supper Object> 对于集合影响
+ 启动jar 需要手动的加载jar包/class文件 -classpath
+ jar包运行需要手动的指定main 入口函数（通常由waven 插件 打包工具完成）
 MANIFEST.MF 文件 Main-Class
+ http抓包? 基于http 抓包需要设置 jvm代理 -DproxySet=true -DproxyHost=127.0.0.1 -DproxyPort=8888
+ 字符串比较 一定要用 "aa".equal("aa")（除非都是字面量），不然就是比较的引用类型
+ string.format("%s %d") 与 .net的 string.format("{0} {1}")
+ ....

#### Web 服务器

+ Jetty/Tomact/Jboss 可以嵌入到应用程序内（Spring Boot 均是采用内嵌方式 友好的支持 微服务/docker）

<!-- more -->

#### Idea

key-promoter 插件方便大家记住快捷键

### Spring

+ IOC 容器(xml方式声明/注解方式声明)
+ AOP(由于类的创建交给了容器 因此很方便实现IOC，**Proxy.newProxyInstance**)
+ `https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html`

#### Spring(IOC)

+ XML方式配置@Bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.1.xsd">
       <!-- 配置一个bean -->
       <bean id="A"  class="test.A" >
            <!--给构造函数传递参数，没有的话则调用默认构造方法  -->
            <constructor-arg value="15"/>
       </bean>
       <bean id="B" class="test.B"></bean>
       <bean id="C" class="test.C">
            <constructor-arg value="22"></constructor-arg>
            <!-- 基本数据类型参数用value=字符串（Spring根据构造参数类型自动解析字符串），
                             引用类型的参数用ref= bean id -->
            <constructor-arg ref="c"></constructor-arg>
       </bean>
       <!-- factory-method通过工厂方法将单例类配置为bean -->
       <bean id="f" class="test.F" factory-method="getImpl">
       </bean>
</beans>
```

+ 注解方式配置@Bean

```java

@ComponentScan 扫描器
@Configuration 表明该类是配置类
@Component   指定把一个对象加入IOC容器--->@Name也可以实现相同的效果【一般少用】
@Repository   作用同@Component； 在持久层使用
@Service      作用同@Component； 在业务逻辑层使用
@Controller    作用同@Component； 在控制层使用
@Resource  依赖关系
如果@Resource不指定值，那么就根据类型来找，相同的类型在IOC容器中不能有两个
如果@Resource指定了值，那么就根据名字来找

@Autowired 注入
--------------------------------
@RestController
public class UserController {
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    @Autowired
    public UserRepository userRepository;

    @PostMapping("/login")
    public String login(@RequestBody User user) {

        User db_user = userRepository.login(user.getUsername(), user.getPassword());
        return jwtTokenUtil.generateToken(db_user);
    }
}


@Repository
public class UserRepositoryImpl implements UserRepository {

    @Override
    public User login(String username, String password) {
        User user = new User();
        user.setUsername(username);
        user.setPassword(password);
        return user;
    }

}

@Component
public class JwtTokenUtil{
    /**
     * 密钥
     */
    private final String secret = "aaaaaaaa";

    /**
     * 从数据声明生成令牌
     *
     * @param claims 数据声明
     * @return 令牌
     */
    private String generateToken(Map<String, Object> claims) {
        Date expirationDate = new Date(System.currentTimeMillis() + 2592000L * 1000);
        return Jwts.builder().setClaims(claims).setExpiration(expirationDate).signWith(SignatureAlgorithm.HS512, secret).compact();
    }
 ....
}
```

#### Spirng AOP

```java
package com.example.controller;  
  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;
@RestController  
public class FirstController {  
  
    @RequestMapping("/aop")  
    public Object first() {  
        return "aop done";  
    } 
}
package com.example.aop;  
  
import org.aspectj.lang.JoinPoint;  
import org.aspectj.lang.ProceedingJoinPoint;  
import org.aspectj.lang.annotation.*;  
import org.springframework.stereotype.Component;  
import org.springframework.web.context.request.RequestContextHolder;  
import org.springframework.web.context.request.ServletRequestAttributes;  
  
import javax.servlet.http.HttpServletRequest;  
import java.util.Arrays;
@Aspect  
@Component  
public class LogAspect {
  //@Pointcut("execution(访问修饰符 返回值匹配 类路径匹配.方法名匹配.*(..))")    
    @Pointcut("execution(public * com.example.controller.*.*(..))")  
    public void webLog(){}  
  
    @Before("webLog()")  
    public void deBefore(JoinPoint joinPoint) throws Throwable {  
    }  
  
    @AfterReturning(returning = "ret", pointcut = "webLog()")  
    public void doAfterReturning(Object ret) throws Throwable {    
    }  
  
    //后置异常通知  
    @AfterThrowing("webLog()")  
    public void throwss(JoinPoint jp){  
    }  
  
    //后置最终通知,final增强，不管是抛出异常或者正常退出都会执行  
    @After("webLog()")  
    public void after(JoinPoint jp){  
    }  
  
    //环绕通知,环绕增强，相当于MethodInterceptor  
    @Around("webLog()")  
    public Object arround(ProceedingJoinPoint pjp) {  
        try {  
            Object o =  pjp.proceed();  
            return o;  
        } catch (Throwable e) {  
            e.printStackTrace();  
            return null;  
        }  
    }  
}  
```

### Waven

#### 常用命令

```cmd
mvn archetype:generate 创建Maven项目
mvn compile
mvn deploy(需要在.m2/setting.xml文件中配置对应的账号与密码)
mvn clean
mvn package
mvn install
mvn clean package -Dmaven.test.skip=true:
```

#### Spring Boot Pom

+ Spring Boot 项目引用

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>beefun.analysis</groupId>
    <artifactId>beefun.analysis</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>

    <!--继承-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.2.RELEASE</version>
    </parent>
    <!--模块-->
    <modules>
        <module>api</module>
        <module>scheduled</module>
        <module>web</module>
        <module>etl</module>
    </modules>
    <!--参数 配置文件中通过 ${java.version} 访问-->
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
        <dependency>
            <groupId>beefun.influx</groupId>
            <artifactId>beefun.influx</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>

    <repositories>
        <repository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </repository>

        <!--私有仓库-->
        <repository>
            <id>nexus</id>
            <name>nexus</name>
            <url>http://172.16.0.241:8081/nexus/content/groups/public/</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    <!--插件仓库-->
    <pluginRepositories>
        <pluginRepository>
            <id>spring-releases</id>
            <url>https://repo.spring.io/libs-release</url>
        </pluginRepository>
    </pluginRepositories>

</project>
```

+ 可执行的jar pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>beefun.analysis</groupId>
    <artifactId>beefun.analysis.etl</artifactId>
    <version>1.0.0</version>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.otter</groupId>
            <artifactId>canal.client</artifactId>
            <version>1.0.12</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-it</artifactId>
            <version>1.3.1</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <!--编译插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>
            <!--打包插件-->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.0.0</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                        <configuration>
                            <!--写入口函数-->
                            <transformers>
                                <transformer
                                        implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>beefun.analysis.etl.EtlApplication</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

+ Maven配置

```xml
    <!-- 仓库账号密码设置/方便发布的私有仓库 -->
<servers>
   <server>
      <id>maven-releases</id>  
      <username>admin</username>  
      <password>admin123</password>  
    </server>
</servers>
     <mirrors>
     <!-- 镜像代理设置，解决大中华局域网的问题 -->
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>*</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
  </mirrors>
```

### Spring Boot

#### 注解/ @Condition***

`http://3mu.github.io/2016/12/19/spring/`

#### 配置

```java
@ConfigurationProperties(prefix = "grafana")
@Component
public class GrafanaConfig {
    private String api;

    public String getApi() {
        return api;
    }

    public void setApi(String api) {
        this.api = api;
    }

    private String token;

    public String getToken() {
        return token;
    }

    public void setToken(String token) {
        this.token = token;
    }

    private List<String> webhooks;


    public List<String> getWebhooks() {
        return webhooks;
    }

    public void setWebhooks(List<String> webhooks) {
        this.webhooks = webhooks;
    }

    private List<String> ignore;


    public List<String> getIgnore() {
        return ignore;
    }

    public void setIgnore(List<String> ignore) {
        this.ignore = ignore;
    }
}
```

```yml
grafana:
  api: http://monitor.wsgjp.com
  token: Bearer eyJrIjoiMWtpSjhJSXFRZzl0TmV6cDBOQjQyRTZRcE42MVNUZmkiLCJuIjoib2tvayIsImlkIjoxfQ==
  webhooks: 
  - https://oapi.dingtalk.com/robot/send?access_token=d3b4bb10db553f92c1f424502cb8cb62c0be8b02bca1add9c4c880d1919eab99
  - https://oapi.dingtalk.com/robot/send?access_token=1231231313123
  ignore: 
  - bfuned2
```

#### Spring Boot MVC

+ thymeleaf 模板解析

```yml
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    mode: HTML5
    encoding: utf-8
    servlet:
      content-type: text/html
    cache: false
```

``` java
@Controller
public class IndexController {
    @RequestMapping(value = "/index")
    public ModelAndView test(ModelAndView mv) {
        mv.setViewName("/index");
        String username = SecurityContextHolder.getContext().getAuthentication().getName();
        mv.addObject("title", "this is a word " + username);
        return mv;
    }
}

```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p th:text="'Hello, ' + ${title}"/><br/>
</body>
</html>
```


#### Spring Boot Rest Service

+ Rest

```java
@RestController
public class ApiController {

    @Autowired
    public OrderService orderService;

    @GetMapping("/count")
    public int getCount() throws SQLException {

        return orderService.getCount();
    }
}
```

#### Spring Boot Schedule

+ 基于corn的自定义任务

```java
@Component
public class KylinTask {

    @Scheduled(cron = "0/2 * * * * ?")
    public void test() {
        System.out.println("is run");
    }
}
```

#### Mybatis 与 Spring Boot 集成

```java
@Mapper
@Component
public interface OrderService {
    @Select("select count(1) from TESTSALE")
    int getCount();
}
```

### 实例演示

`http://gitlab.wsgjp.com.cn:10080/wsgjp/analysis.git`

#### Kylin

![123](http://ohugkp1mj.bkt.clouddn.com/%E6%95%B0%E6%8D%AE%E6%94%B6%E9%9B%86.png)

+ EFP 连接数据分析服务获取报表数据
+ 数据分析服务通过Sql 语句查询kylin 系统数据
+ 计划任务 每天晚上调用Kylin Rest Api 增量的进行预计算 方便查询
+ 数据收集 通过Canal 经过数据清洗导入到Hbase

#### 项目结构

+ beefun.analysis.api

对erp提供api服务

+ beefun.analysis.etl

数据收集 通过canal 采集binlog 同步到 hbase

+ beefun.analysis.scheduled

计划任务 定期执行kylin 构建任务

#### 资料

`https://spring.io/guides`

`http://www.mybatis.org/mybatis-3/zh/index.html`

`https://github.com/dyc87112/SpringBoot-Learning`

`《thinking in java》`
