---
title: jstack 整理
date: 2018-10-23 10:51:54
tags: CPU 问题排查
---

# CPU问题

## 查看CPU高的进程ID

```shell
//查看cpu高的进程id
top
//查看进程内线程CPU占用情况
ps -mp 26178 -o THREAD,tid,time
//将线程ID转换为16位
printf '%x\n' 26181
//查看堆栈
jstack pid | grep tid -A60
```