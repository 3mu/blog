---
title: docker
date: 2019-04-19 12:41:07
tags:
---

# docker 备忘

## set docker Repository

```shell
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
```

## install

``` shell
sudo yum install docker-engine

```

## dockerfile

```shell
FROM openjdk:8
ARG JAR
ENV JAVA_OPTS=""
COPY ${JAR} app.jar
ENTRYPOINT java ${JAVA_OPTS} -Djava.security.egd=file:/dev/./urandom -jar /app.jar
```

## docker build

```shell
--net=host 使用宿主机IP + Host
docker build -t nacos-provider:0.0.4 .  --build-arg JAR=provider-0.0.1-SNAPSHOT.jar  -f  Dockerfile
```


## docker run

``` shell
docker pull nginx
docker run --name nacos-nginx -d -p 8848:80  -v /opt/nginx/nginx.conf/nginx.conf:/etc/nginx/nginx.conf  -v /opt/nginx/logs:/var/log/nginx -v /opt/nginx/logs:/etc/nginx/logs -d docker.io/nginx

docker run --name p3 -e JAVA_OPT="-Dserver.port=998" -v /opt/docker/provider/application.yml:/application.yml -v /opt/docker/provider/log/:/log/ -d --network=host --privileged 172.16.1.103:5000/p1

```



## docker ftp

```shell
vim /usr/lib/systemd/system/docker.service
#修改ExecStart这行
ExecStart=/usr/bin/dockerd  -H tcp://0.0.0.0:2375  -H unix:///var/run/docker.sock
#重新加载配置文件
systemctl daemon-reload
#重启服务
systemctl restart docker.service
#查看端口是否开启
netstat -nlpt
#直接curl看是否生效
curl http://127.0.0.1:2375/info
```


## docker 私有 registry

```shell
docker pull registry

vim /data/config.yml
version: 0.1
log:
  fields:
    service: registry
storage:
  delete:
    enabled: true
  cache:
    blobdescriptor: inmemory
  filesystem:
    rootdirectory: /var/lib/registry
http:
  addr: :5000
  headers:
    X-Content-Type-Options: [nosniff]
health:
  storagedriver:
    enabled: true
    interval: 10s
    threshold: 3

docker run -d -p 5000:5000 -v /opt/data/registry:/var/lib/registry  -v /data/config.yml:/etc/docker/registry/config.yml  registry 
docker pull busybox
docker tag busybox 192.168.0.153:5000/busybox
docker push 192.168.0.153:5000/busybox
vim  /usr/lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd  --insecure-registry 192.168.0.153:5000
systemctl daemon-reload
systemctl restart docker
docker push 192.168.0.153:5000/busybox
docker pull 192.168.0.153:5000/busybox
```