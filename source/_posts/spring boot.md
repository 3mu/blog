---
title: Spring Boot 重启
date: 2018-08-01 10:51:54
tags: Spring
---

#

## 打包插件配置

```xml
   <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <executable>true</executable>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

## 构建服务

```shell
# 设置服务关联
ln -s /opt/app/monitor/xx.jar  /etc/init.d/xx
# 查看关联结果
ls /etc/init.d/
# jvm配置项
vim /opt/app/monitor/xx.conf
# 服务命令
service xx start|restart|stop|status
```

<!-- more -->