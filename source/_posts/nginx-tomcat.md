---
title: nginx_tomcat(转)
date: 2016-12-15 12:45:39
tags: tomact
---
# 搭建Nginx+Java环境

Apache对Java的支持很灵活，它们的结合度也很高，例如Apache+Tomcat和Apache+resin等都可以实现对Java应用的支持。Apache一般采用一个内置模块来和Java应用服务器打交道。与Apache相比，Nginx在配合Java应用服务器方面，耦合度很低，它只能通过自身的反向代理功能来实现与Java应用服务器的支持。但这恰恰是Nginx的一个优点，耦合度的降低，可以使Nginx与Java服务器的相互影响降到最低。

接下来通过Nginx+Tomcat的实例来讲解Nginx对Java的支持。Tomcat在高并发环境下处理动态请求时性能很低，而在处理静态页面更加脆弱。虽然Tomcat的最新版本支持epoll，但是通过Nginx来处理静态页面要比通过Tomcat处理在性能方面好很多。

Nginx可以通过以下两种方式来实现与Tomcat的耦合：

将静态页面请求交给Nginx，动态请求交给后端Tomcat处理。

将所有请求都交给后端的Tomcat服务器处理，同时利用Nginx自身的负载均衡功能进行多台Tomcat服务器的负载均衡。

下面通过两个配置实例分别讲述这两种实现Nginx与Tomcat耦合的方式
## 动态页面与静态页面分离的实例
```config
server {  
      listen 80;  
      server_name www.ixdba.net;  
      root /web/www/html;  
 
location /img/ {  
      alias /web/www/html/img/;  
}  
 
location ~ (\.jsp)|(\.do)$ {  
     proxy_pass http://192.168.12.130:8080;  
     proxy_redirect off;  
     proxy_set_header Host $host;  
     proxy_set_header X-Real-IP $remote_addr;  
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
     client_max_body_size 10m;  
     client_body_buffer_size 128k;  
     proxy_connect_timeout 90;  
     proxy_send_timeout 90;  
     proxy_read_timeout 90;  
     proxy_buffer_size 4k;  
     proxy_buffers 4 32k;  
     proxy_busy_buffers_size 64k;  
     proxy_temp_file_write_size 64k;  
 } 
} 
```
在这个实例中，首先定义了一个虚拟主机www.ixdba.net，然后通过location指令将/web/www/html/img/目录下的静态文件交给Nginx来完成。最后一个location指令将所有以.jsp、.do结尾的文件都交给Tomcat服务器的8080端口来处理，即http://192.168.12.130:8080。

需要特别注意的是，在location指令中使用正则表达式后，proxy_pass后面的代理路径不能含有地址链接，也就是不能写成http://192.168.12.130:8080/，或者类似http://192.168.12.130:8080/jsp的形式。在location指令不使用正则表达式时，没有此限制。
## 多个Tomcat负载均衡的实例
这里假定有3台Tomcat服务器，分别开放不同的端口，地址如下：
```config
192.168.12.131:8000  
192.168.12.132:8080  
192.168.12.133:8090 
```
Nginx的相关配置代码如下：
```config
upstream mytomcats {  
      server 192.168.12.131:8000;  
      server 192.168.12.132:8080;  
      server 192.168.12.133:8090;  
}  
 
server {  
      listen 80;  
      server_name www.ixdba.net;  
 
location ~* \.(jpg|gif|png|swf|flv|wma|wmv|asf|mp3|mmf|zip|rar)$ {  
       root /web/www/html/;  
}  
 
location / {  
          proxy_pass http://mytomcats;  
          proxy_redirect off;  
          proxy_set_header Host $host;  
          proxy_set_header X-Real-IP $remote_addr;  
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
          client_max_body_size 10m;  
          client_body_buffer_size 128k;  
          proxy_connect_timeout 90;  
          proxy_send_timeout 90;  
          proxy_read_timeout 90;  
          proxy_buffer_size 4k;  
          proxy_buffers 4 32k;  
          proxy_busy_buffers_size 64k;  
          proxy_temp_file_write_size 64k;  
}  
 
} 
```
在这个实例中，先通过upstream定义一个负载均衡组，组名为mytomcats，组的成员就是上面指定的3台Tomcat服务器；接着通过server指令定义一个www.ixdba.net的虚拟主机；然后通过location指令以正则表达式的方式将指定类型的文件全部交给Nginx去处理；最后将其他所有请求全部交给负载均衡组来处理。

这里还有一点需要注意，如果在location指令使用正则表达式后再用alias指令，Nginx是不支持的。