---
title: consul集群搭建
date: 2018-07-17 10:51:54
tags: hexo
---

# consul集群搭建

## 环境

+ centos 7 
+ K1/K2/K3 三台PC

## 下载zip包

https://www.consul.io/downloads.html

## 安装

+ scp consul /usr/local/bin/

+ consul -v

## 启动加入集群

```shell
consul agent -server -bootstrap -bind=0.0.0.0 -client=172.16.3.51 -data-dir=/opt/consul/data -ui -node=k1 &
consul agent -server -bind=0.0.0.0 -client=172.16.3.52 -data-dir=/opt/consul/data -ui -node=k2  -join 172.16.3.51 &
consul agent -server -bind=0.0.0.0 -client=172.16.3.49 -data-dir=/opt/consul/data -ui -node=k3  -join 172.16.3.51 &
```

## 访问UI

http://172.16.3.51:8500/ui/