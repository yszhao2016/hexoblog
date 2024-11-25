---
title: nginx-配置文件
abbrlink: 2bee8b90
categories:
  - 服务器相关
  - nginx
date: 2023-07-27 14:17:43
tags:
---
# 一 、配置详解

## 1.1nginx配置文件
    #nginx 已那个用户身份访问文件
    user  www;  
    
    #指定nginx使用的worker进程数
    worker_processes 8;   
    
    #worker_cpu_affinity是一个用于配置工作进程CPU亲和性的选项。
    #CPU亲和性是指将进程或线程绑定到特定的CPU核心上，以提高性能和效率。
    #通常cpu几核，就配置几个
    worker_cpu_affinity 0000001 00000010 00000011 00000100 00000101 00000110 00000111 00001000;
    
    #PID配置项是用于指定保存主进程ID的文件路径
    pid        /var/run/nginx.pid;
    
    #用于限制每个worker进程可以打开的最大文件描述符数量。
    #ulimit -n 查看系统文件句柄数   设置为系统允许的最大文件句柄数的 80% - 90%
    worker_rlimit_nofile 65535;
    
    
    #用于控制Nginx服务器的事件模型和并发处理方式。
    events {
        #用于设置每个worker进程的最大并发连接数 即每个worker进程可以同时处理的最大客户端连接数
        worker_connections 1024;
        
        #常见的事件模型包括epoll、kqueue和select等。
        use epoll;
        
        #用于指定是否允许一个worker进程同时接受多个新连接
        multi_accept on;
    }
    
    #用于代理TCP和UDP流量，允许将Nginx作为代理服务器来处理网络流量
    #使用stream模块可以使Nginx具备代理TCP和UDP流量的能力
    stream {
           include /etc/nginx/stream/*.conf;
    }
    
    #此模块用于配置HTTP服务器，允许主机作为Web服务器来处理HTTP请求
    http {
        #引入mime头文件  定义了Nginx的mime类型    
        include       mime.types;
        
        #指令用于设置在请求无法匹配到任何MIME类型时的默认类型
        default_type  application/octet-stream;
        
        #定义日志格式  main 为日志格式 名称
        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';
                          
        #定义访问日志路径 main 指用那个日志格式名称                  
        access_log  /var/log/nginx/access.log  main;
        
        # 当将sendfile设置为on时，Nginx使用sendfile()系统调用来直接将文件从磁盘传输到网络，
        # 而无需将文件数据复制到用户空间。这样可以显着提高文件传输速度和效率。
        sendfile on;
        
        #将tcp_nopush设置为on时，Nginx会禁用TCP_CORK
        #允许在nginx worker进程发送数据时立即将响应发送到客户端，而无需等待TCP缓冲区填满。
        #将tcp_nopush设置为on可以提高响应的实时性，允许立即发送数据到客户端
        tcp_nopush on;
        
        #将tcp_nodelay设置为on时，Nginx会打开TCP_NODELAY，这将禁用TCP的Nagle算法，
        #以便在发送小数据包或有延迟要求的数据时能够及时发送数据。
        #将tcp_nodelay设置为on可以减少TCP延迟，适用于实时性要求高的应用场景。
        tcp_nodelay on;
        
        #隐藏Nginx版本号
        server_tokens  off;    

        proxy_hide_header X-Powered-By;
        
        proxy_hide_header Server;
         
        #配置空主机头 404 就是禁用http通过ip访问
        
        server {
            listen 80 default_server;
            server_name _;
            return 444;
        }
        server {
            listen 443 ssl default_server;
            server_name _;
            ssl on;
            return 444;
        }
        
        include /etc/nginx/conf.d/*.conf;
    }
    
## 1.2log_format可用变量

    $remote_addr：客户端的IP地址。
    $remote_user：客户端的用户名称（如果有授权）。
    $time_local：访问时间和日期（格式：[day/month/year:hour:minute:second zone]）。
    $request_method：HTTP请求的方法（例如GET或POST）。
    $request_uri：完整的请求URI。
    $request_length：请求的长度（包括请求行，请求头和请求体）。
    $status：HTTP响应的状态码。
    $body_bytes_sent：发送给客户端的响应体的字节数。
    $http_referer：请求中的“Referer”头部字段，包含了前一个页面的URL。
    $http_user_agent：请求的用户代理头部字段，表示发起请求的客户端工具或库。
## 1.3host配置文件
    
### upstream 模块    
    #定义负载均衡
    #放置在 http 或 server 块
    upstream name {
        
         #用于指定上游服务器的地址和端口。可以指定多个server来定义多个上游服务器。
         server backend1.example.com;
         
         #max_fails 和 fail_timeout：用于设置上游服务器的故障处理策略
         #max_fails 指定在多少次失败请求后将服务器标记为不可用
         #fail_timeout 指定在服务器被标记为不可用后，多长时间内不再尝试请求。
         server 127.0.0.1:8080 max_fails=3 fail_timeout=30s;
        
        #设置与上游服务器的连接保持活动的时间。可以提高性能并减少连接建立的开销。
        keepalive:32;

    }

    #weight：用于设置服务器的权重。权重越高，Nginx 转发给该服务器的请求越多。
    #示例：
    upstream backend {
        server backend1.example.com weight=3;
        server backend2.example.com weight=2;
    }



    #ip_hash：使用客户端的IP地址进行负载均衡，使得来自同一IP地址的请求始终被转发到同一台上游服务器。
    #示例：
    upstream backend {
        ip_hash;
        server backend1.example.com;
        server backend2.example.com;
    }
    
### Server模块
    server：定义一个虚拟主机
    server{
        listen：指定服务器监听的端口
        server_name：配置域名或IP地址
        root：指定网站文件的根目录
        index：定义当访问网站时默认显示的文件
        location：定义请求的URL匹配规则和对应的处理方法。
        proxy_pass：用于反向代理服务器的配置。
        ssl_certificate和ssl_certificate_key：配置SSL/TLS证书。
        try_files：定义文件检查规则
        
        gzip on;    将 Gzip 压缩功能开启。
        
        将所有类型的文件都进行 Gzip 压缩。这意味着无论是文本文件、图像文件还是视频文件，
        都将进行压缩传输。如果你希望只压缩特定类型的文件，可以指定具体的文件类型，
        例如 gzip_types text/plain text/css application/javascript;。
        gzip_types *;
       
        (gzip_proxied on or gzip_proxied off or gzip_proxied expired no-cache no-store private auth) 指定需要压缩的响应类型，
        并且设置允许或者不允许使用代理服务器进行压缩。any 表示无论何时都进行压缩。
        gzip_proxied any;
    }

# 二、常用示例
    
## 2.1 mysql 代理示例    
    stream {
            server {
                listen 63306;
                proxy_connect_timeout 8s;
                proxy_timeout 24h;
                proxy_pass 192.168.0.108:3306;
            }
    }
    
    
##  2.3 带upstream的写法  
    upstream rd_servers {
      server 127.0.0.1:5000;
    }
    
    server{
      server_tokens off;
      listen 80;
      server_name redash.xxxxx.com;
      access_log /var/log/nginx/rd.access.log;
    
      gzip on;
      gzip_types *;
      gzip_proxied any;
    
      location / {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass       http://rd_servers;
      }
    }
  

  
