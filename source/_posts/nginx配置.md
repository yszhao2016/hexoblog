---
title: nginx配置
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
        
        
        #配置空主机头 404 就是禁用http通过ip访问
        server {
           listen 80 default;
           server_name _;
           root html;
           location / { return 404;}
           location ~ /.ht { deny all; }
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

# 常用示例
    
## mysql 代理示例    
    stream {
            upstream mysql {
                  server 192.168.0.108:3306;
             }
              
            server {
                listen 0.0.0.0:63306;
                proxy_connect_timeout 8s;
                proxy_timeout 24h;
                proxy_pass mysql;
            }
    }

  
