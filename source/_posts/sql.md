title: mysql SHOW PROCESSLIST
date: 2016-12-11 13:38:31
tags:
---
# SHOW PROCESSLIST

**各列的含义和用途：**

* ID列

> 一个标识，你要kill一个语句的时候很有用，用命令杀掉此查询 /*/mysqladmin kill 进程号。

* user列

> 显示单前用户，如果不是root，这个命令就只显示你权限范围内的sql语句。

* host列

> 显示这个语句是从哪个ip的哪个端口上发出的。用于追踪出问题语句的用户。

* db列

> 显示这个进程目前连接的是哪个数据库。

* command列

> 显示当前连接的执行的命令，一般就是休眠（sleep），查询（query），连接（connect）。

* time列

> 此这个状态持续的时间，单位是秒。

* state列

> 显示使用当前连接的sql语句的状态，很重要的列，后续会有所有的状态的描述，请注意，state只是语句执行中的某一个状态，一个 sql语句，以查询为例，可能需要经过copying to tmp table，Sorting result，Sending data等状态才可以完成

* info列

> 显示这个sql语句，因为长度有限，所以长的sql语句就显示不全，但是一个判断问题语句的重要依据。
<!-- more -->
## QUERY
```mysql
select user,host,info from information_schema.processlist WHERE ....
```
