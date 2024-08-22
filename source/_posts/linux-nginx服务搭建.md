---
title: 搭建 nginx_server
categories:
  - 服务器相关
abbrlink: fa953e4f
date: 2022-04-17 22:53:07
tags:
---

# 安装nginx

## 1.yum 安装（默认情况下使用yum安装的nginx包含ssl模块）
        
        官方 yum 源链接 http://nginx.org/en/linux_packages.html#RHEL-CentOS
        
        vim /etc/yum.repos.d/nginx.repo
        ```
        [nginx-stable]
        name=nginx stable repo
        baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
        gpgcheck=1
        enabled=1
        gpgkey=https://nginx.org/keys/nginx_signing.key
        module_hotfixes=true
        
        [nginx-mainline]
        name=nginx mainline repo
        baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=https://nginx.org/keys/nginx_signing.key
        module_hotfixes=true
        ```
        
        yum-config-manager --enable nginx-stable
        
        yum install nginx


## 2.源码编译安装

    下载源码包  http://nginx.org/en/download.html  下载稳定版本
    
    Mainline version：Mainline 是 Nginx 目前主力在做的版本，可以说是开发版
    Stable version：最新稳定版，生产环境上建议使用的版本
    Legacy versions：遗留的老版本的稳定版
    
    下载命令
    wget http://nginx.org/download/nginx-1.22.0.tar.gz
    
    解压缩
    tar -zxvf nginx-1.22.0.tar.gz
        
     ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre
     
     make && make install
     
     ln -s /usr/local/nginx/conf  /etc/nginx
     
     
     
# 配置服务

    vim /lib/systemd/system/nginx.service     
         
     ```
    [Unit]
    Description=nginx
    After=network.target
    
    [Service]
    Type=forking
    ExecStart=/usr/local/nginx/sbin/nginx
    ExecReload=/usr/local/nginx/sbin/nginx -s reload
    ExecStop=/usr/local/nginx/sbin/nginx -s quit
    PrivateTmp=true
    
    [Install]
    WantedBy=multi-user.target
    ```
    
    软连接etc目录
    ln -s /usr/local/nginx/conf /etc/nginx   
    
    systemctl status nginx
    systemctl reload nginx
    systemctl start nginx
    
    开机启动
    systemctl enable nginx
    
    添加用户
    groupadd www
    useradd -g www -s /sbin/nologin www  
    
# 配置文件  
###### /etc/nginx/nginx.conf 
       
 ```
    user  www;
    #cpu 几核 这进程就多少
    worker_processes  2;
    worker_cpu_affinity 01 10;
    
    worker_rlimit_nofile 65535;  # 一般等于ulimit -n系统值
    
    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;
    
    #pid        logs/nginx.pid;
    
    
    events {
        worker_connections  10240;
        use epoll;
    }
    
    
    http {
        include       mime.types;
        default_type  application/octet-stream;
        server_tokens off;
    
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';
    
        #access_log  logs/access.log  main;
    
        sendfile        on;
        #tcp_nopush     on;
    
        #keepalive_timeout  0;
        keepalive_timeout  65;
    
        gzip on;       #表示开启压缩功能
        gzip_min_length  1k; #表示允许压缩的页面最小字节数，页面字节数从header头的Content-Length中获取。默认值是0，表示不管页面多大都进行压缩，建议设置成大于1K。如果小于1K可能会>越压越大
        gzip_buffers     4 32k; #压缩缓存区大小
        gzip_http_version 1.1; #压缩版本
        gzip_comp_level 6; #压缩比率， 一般选择4-6，为了性能
        gzip_types text/css text/xml application/javascript;
        #指定压缩的类型 gzip_vary on;　#vary header支持
    
    
        #配置空主机头 404
        server {
            listen 80 default;
            server_name _;
            root html;
            location / { return 404;}
            location ~ /.ht { deny all; }
    
        }
        include vhost/*.conf;
    }
```  
 
    
###### vhost/*.conf   

    server{
        listen 80;
        server_name tp.webstudy.cc;
        set $wwwroot /home/www/htdocs/tp.webstudy.cc;
        root $wwwroot;
        access_log  logs/ai.access.log;
        error_log logs/ai.error.log debug;

        #ssl_certificate     /usr/local/nginx/conf/ssl/jinju.utooo.com/Nginx/1_jinju.utooo.com_bundle.crt;
        #ssl_certificate_key /usr/local/nginx/conf/ssl/jinju.utooo.com/Nginx/2_jinju.utooo.com.key;
        #ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

        location / {
            index  index.php index.html index.htm;
            # 这里使用try_files进行url重写，不用rewrite了
            # try_files $uri $uri/ /index.php?$query_string;
        }

       #error_page   500 502 503 504  /50x.html;
       location = /50x.html {
            root   html;
        }
        location ~ .*\.php{
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME   $document_root$fastcgi_script_name;
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
        }

    }
   
    
      
