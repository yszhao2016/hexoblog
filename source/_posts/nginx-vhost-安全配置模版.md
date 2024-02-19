---
title: nginx-vhost-安全配置模版
abbrlink: 8a9d1567
categories:
  - 服务器相关
date: 2023-07-13 19:11:30
tags:
---
#nginx-vhost-安全配置模版

    server{
            listen 443 ssl ;
            server_name  [域名或者ip];
            index        index.php index.html index.htm;
            root [项目路径];
            access_log  /var/log/nginx/[项目名称].access.log;
            error_log   /var/log/nginx/[项目名称].error.log;
    
            #证书文件名称
            ssl_certificate [证书路径 公钥];
            #私钥文件名称
            ssl_certificate_key [证书路径 私钥];
            ssl_session_timeout 5m;
            #请按照以下协议配置
            ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
            #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
            ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
            ssl_prefer_server_ciphers on
    
    
            #允许cookie
            add_header 'Access-Control-Allow-Credentials' 'true';
            #安全头
            add_header X-Xss-header "1;mode=block";
            add_header Set-Cookie "HttpOnly";
            add_header Set-Cookie "Secure";
            add_header X-Frame-Options "SAMEORIGIN";
    
            location ~ \.(json|lock|git)$ {
                     deny all;
            }
            location ~ ^/(uploads|assets)/.*\.(php|php5|jsp|shtml)$ {
                deny all;
            }
            location ~ .*\.(txt|doc|pdf|rar|gz|zip|docx|exe|xlsx|ppt|pptx)$ {
                add_header Content-Disposition attachment;
            }
            location =/assets/libs/bootstrap/Gemfile {
                           return 404;
            }
            location ~ /\. {
                deny  all;
            }
    
            location / {
                    proxy_cookie_path / "/; httponly; secure; SameSite=None";
                    if (!-e $request_filename) {
                            rewrite  ^(.*)$  /index.php?s=/$1  last;
                            break;
                    }
    
            }
    
            location ~ \.php(.*)$ {
                    fastcgi_pass   [访问php方式];
                    fastcgi_index  index.php;
                    fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
                    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
                    fastcgi_param  PATH_INFO  $fastcgi_path_info;
                    fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
                    include        fastcgi_params;
            }
    
            #timeout
            large_client_header_buffers 4 16k;
            client_max_body_size 180m;
            client_body_buffer_size 128k;
            fastcgi_connect_timeout 600;
            fastcgi_read_timeout 600;
            fastcgi_send_timeout 600;
            fastcgi_buffer_size 128k;
            fastcgi_buffers   2 256k;
            fastcgi_busy_buffers_size 256k;
            fastcgi_temp_file_write_size 256k;
            proxy_read_timeout  240s;
    }