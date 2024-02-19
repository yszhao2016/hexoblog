---
title: web安全
abbrlink: def64e49
date: 2023-07-09 14:16:41
tags:
---

# php安全配置

    主要控制php执行危险函数，默认是关闭：Off
    sql.safe_mode=On
    禁止显示PHP版本号
    expose_php = Off
    关闭重定向执行php文件（你的网站url/as=你的网站url/sdf/muma.php）
    cgi.force_redirect = 0
    禁止解析非法php文件
    cgi.fix_pathinfo = 0
    关闭错误信息输出
    display_error = Off
    error_reporting = E_WARNING & E_ERROR
    记录错误日志至后台, 方便追溯
    log_errors = On
    error_log = /var/log/php_error.log
    禁止远程执行phpshell
    allow_url_include = Off
    静止file_get_content函数获取资源
    allow_url_fopen = Off
    格式化时间
    date.timezone=Asia/Shanghai
    
    
# nginx安全配置    

    add_header X-Content-Type-Options nosniff;
    add_header 'Referrer-Policy' 'origin';
    add_header X-Download-Options "noopen" always;
    add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
    add_header X-Permitted-Cross-Domain-Policies  "master-only";
    add_header X-Frame-Options SAMEORIGIN;
    #add_header Content-Security-Policy "default-src 'self' data: *.xxx.com  'unsaf
    add_header X-XSS-Protection "1; mode=block";
    # add_header Content-Security-Policy "default-src *;style-src 'self' 'unsafe-inline';script-src 'self' 'unsafe-inline' 'unsafe-eval';img-src * data:;worker-src * blob:;font-src 'self' data:;"
    
    
    location / {
        if ($http_origin ~* (https?://[^/]*\.kuaidasoft\.com)){
                  add_header 'Access-Control-Allow-Origin' "$http_origin";
        }
        try_files $uri $uri/ /index.html;
    }  
    
    
    
    #指定允许跨域的方法，*代表所有
    add_header Access-Control-Allow-Methods 'GET,PUT,POST,DELETE,OPTIONS';
    
    # 预检命令的缓存，如果不缓存每次会发送两次请求
    add_header Access-Control-Max-Age 3600;
    
    #带cookie请求需要加上这个字段，并设置为true
    add_header Access-Control-Allow-Credentials true;
    
    #   表示允许这个域跨域调用（客户端发送请求的域名和端口） 
    #   $http_origin动态获取请求客户端请求的域   不用*的原因是带cookie的请求不支持*号
    #add_header Access-Control-Allow-Origin $http_origin;
    #   表示请求头的字段 动态获取
    add_header Access-Control-Allow-Headers $http_access_control_request_headers;
    
    
    
 # mysql   


