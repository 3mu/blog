---
title: webmagic 搭建
date: 2017-07-10 06:16:13
tags: webmagic
---
# 简介
![](http://code4craft.github.io/images/posts/webmagic.png)
+ WebMagic是一个简单灵活的Java爬虫框架。基于WebMagic，你可以快速开发出一个高效、易维护的爬虫。
+ 简单的API，可快速上手
+ 模块化的结构，可轻松扩展.WebMagic的结构分为Downloader、PageProcessor、Scheduler、Pipeline四大组件，
+ 提供多线程和分布式支持(官方提供redis分布式支持)
<!-- more -->
# 开发环境搭建
+ pom 引用
```xml
   <dependencies>
        <!--核心库-->
        <dependency>
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-core</artifactId>
            <version>0.7.2</version>
        </dependency>
        <!--扩展 包 redis分布式支持...-->
        <dependency>
            <groupId>us.codecraft</groupId>
            <artifactId>webmagic-extension</artifactId>
            <version>0.7.2</version>
        </dependency>
    </dependencies>
```
+ pom 插件(指定 jar 启动class)

```xml
 <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>
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
                            <transformers>
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>com.es.core.App</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
```
## 爬美团技术博客文章
+ 定义pageprocessor
```java
public class MeituanPageProcessor implements PageProcessor {

    private Site site = Site.me().setRetryTimes(3).setSleepTime(1000);

    @Override
    public void process(Page page) {
        List<String> targets=new ArrayList<String>();
        Selectable links=page.getHtml().links();
        for(String link : links.all())
        {
           if (link.contains("tech.meituan.com")&&link.contains(".html")){
               targets.add(link);
           }
        }
        /// 相关链接
        page.addTargetRequests(targets);
        System.out.println(page.getUrl());
    }

    @Override
    public Site getSite() {
        return site;
    }
}

```
+ 启动
```java

public  class  App{
    public static void main(String[] args) {
        InputStream stream=App.class.getClassLoader().getResourceAsStream("app.properties");
        Properties properties=new Properties();
        try {
            properties.load(stream);
        } catch (IOException e) {
            e.printStackTrace();
        }
        String host=properties.getProperty("redis.config.host");
        int port= Integer.parseInt(properties.getProperty("redis.config.port"));
        Spider spider=Spider.create(new MeituanPageProcessor());
        GenericObjectPoolConfig config=new GenericObjectPoolConfig();
        JedisPool pool=new JedisPool(config,host,port,10000,"********",false);
        spider.setScheduler(new RedisScheduler(pool));
        spider.addUrl("http://tech.meituan.com/");
        spider.thread(5);
        spider.run();
    }
}
```
## 运行
```
mvn install
java -jar com.es-1.0-SNAPSHOT.jar
```
