---
title: Greenplum 源码编译安装教程
date: 2018-07-17 10:51:54
tags: Greenplum
---

# 安装环境准备

## 处理/etc/sysctl.conf

```vim
kernel.shmall = 4000000000
net.ipv4.tcp_max_syn_backlog = 4096
vm.overcommit_memory = 2
net.core.netdev_max_backlog = 10000
kernel.sem = 250 64000 100 512
net.ipv4.ip_local_port_range = 1025 65535
net.ipv4.tcp_tw_recycle = 1
```

## 处理/etc/security/limits.conf

```vim
* soft nofile 65536
* hard nofile 65536
```

## /etc/security/limits.d/90-nproc.conf

```vim
* soft nproc 131072
* hard nproc 131072
```

<!-- more -->

## yum安装依赖

```shell
 sudo yum -y install rsync coreutils glib2 lrzsz sysstat e4fsprogs xfsprogs ntp readline-devel zlib zlib-devel openssl openssl-devel pam-devel libxml2-devel libxslt-devel python-devel tcl-devel gcc make smartmontools flex bison perl perl-devel perl-ExtUtils* OpenIPMI-tools openldap openldap-devel logrotategcc-c++ python-py curl-devel pam

 sudo yum install apr-devel libevent-devel libyaml-devel
 wget https://bootstrap.pypa.io/get-pip.py
 sudo python get-pip.py
 sudo pip install psi lockfile paramiko setuptools epydoc
 sudo pip install --upgrade setuptools

```

## 编译安装

```
./configure--prefix=/home/gpadmin/gpsql --with-gssapi --with-pgport=5432--with-libedit-preferred --with-perl --with-python --with-openssl --with-pam--with-krb5 --with-ldap --with-libxml --enable-cassert --enable-debug --enable-testutils--enable-debugbreak --enable-depend
```