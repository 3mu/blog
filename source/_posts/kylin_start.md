---
title: kylin 部署_启动流程
date: 2018-07-17 10:51:54
tags: kylin,bigdata
---

# ssh 免密登录

## matser 设置

```shell
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600  ~/.ssh/authorized_keys
sudo scp ~/.ssh/id_rsa.pub hadoop@slave1:~/ 
```

## host 更改

```code
172.16.3.51 K1
172.16.3.50 K2
172.16.3.49 K3
```

## 安装jdk

## 创建data目录

```shell
mkdir /home/data/hdfs/name
mkdir /home/data/hdfs/data
```

## haddoop 配置项

/opt/hadoop/hadoop-2.8.4/etc/hadoop/slaves

```code
K2
K3
```

hdfs-site.xml

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/home/data/hdfs/name</value>
    <final>true</final>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/home/data/hdfs/data</value>
    <final>true</final>
  </property>
  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>K1:9001</value>
  </property>
</configuration>

```

core-site.xml

```xml

<configuration>
 <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/home/data/hdfs/tmp</value>
    <description>A base for other temporary directories.</description>
  </property>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://K1:9000</value>
  </property>
</configuration>

```

mapred-site.xml

```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
     <name>mapreduce.jobhistory.address</name>
     <value>K1:10020</value>
  </property>
</configuration>

```

yarn-site.xml

```xml
<configuration>

<!-- Site specific YARN configuration properties -->
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>K1</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
　　<name>yarn.log-aggregation-enable</name>
　　<value>true</value>
　</property>
　<property>
  　<name>yarn.log-aggregation.retain-seconds</name>
　　<value>604800</value>
　</property>
</configuration>

```

## 运行Hadoop

首先使用命令：hadoop namenode -format

运行:start-all.sh,start-hdfs.sh, start-yard.sh

启动 historystoryserive

mr-jobhistory-daemon.sh start historyserver 


## 搭建ZK 集群

```conf
server.1=K1:2888:3888
server.2=K2:2888:3888
server.3=K3:2888:3888
```

myid设置

echo 1 >/home/data/zookeeper/myid

## 搭建Hbase 集群

regionservers

```code
K2
K3
```

core-site.xml

```xml
<configuration>
 <property>
    <name>hadoop.tmp.dir</name>
    <value>file:/home/data/hdfs/tmp</value>
    <description>A base for other temporary directories.</description>
  </property>
  <property>
    <name>io.file.buffer.size</name>
    <value>131072</value>
  </property>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://K1:9000</value>
  </property>
  <property>
    <name>hadoop.proxyuser.root.hosts</name>
    <value>*</value>
  </property>
  <property>
    <name>hadoop.proxyuser.root.groups</name>
    <value>*</value>
  </property>
</configuration>

```

hdfs-site.xml

```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/home/data/hdfs/name</value>
    <final>true</final>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/home/data/hdfs/data</value>
    <final>true</final>
  </property>
  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>K1:9001</value>
  </property>
  <property>
    <name>dfs.webhdfs.enabled</name>
    <value>true</value>
  </property>
  <property>
    <name>dfs.permissions</name>
    <value>false</value>
  </property>
</configuration>

```

hbase-site.xml

```xml
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://K1:9000/hbase</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.master</name>
    <value>K1:60000</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>K1,K2,K3</value>
  </property>
</configuration>

```

## hive 配置

## kylin 配置