---
title: .Net多线程下http并发数设置
date: 2016-12-19 15:04:26
tags: .Net
---

```java
System.Net.ServicePointManager.DefaultConnectionLimit =512 ;
```
或者
```xml
  <system.net>
    <connectionManagement>
      <add address="*" maxconnection="512"/>
    </connectionManagement>
  </system.net>
```
<!--more-->