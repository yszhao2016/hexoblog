---
title: windows搭建apache+php+mysql5.6
date: 2023-02-25 11:51:34
tags:
---
# 安装mysql服务
    
    1.下载地址：https://downloads.mysql.com/archives/community/
    
    2.下载相应windows版本
    
    3.解压后cmd cd到文件目录mysqld.exe  执行   mysqld.exe install【mysqld remove 移除服务 mysqld start 启动服务】
    
    4.设置开机启动  然后再win+r 输入services.msc  打开后找到mysql 右键点击属性   将启动类型设置为自动即可
    
    【注：期间不同版本可能会报奇奇怪怪的错，百度安装相关的版本vc库】

# 安装php

    1.下载地址：https://windows.php.net/downloads/releases/archives/
    
    2.下载版本必须有  php7apache2_4.dll  vc版本最好跟apache的版本一样
    
    3.配置php.ini 开启相应 扩展
    
    4.测试是否能用  到相应目录 执行 php.exe -v
    
    
    【注：期间不同版本可能会报奇奇怪怪的错，百度安装相关的版本vc库】
    
    
    

# 安装apache 服务器

##apache 安装对接php
    下载：https://www.apachelounge.com/download/VC14/
    
    
    到apache/config/httpd.conf文件
    找到<IfModule unixd_module>配置行 在其上面添加
    
    LoadModule php7_module C:/php/php7apache2_4.dll  【php7apache2_4.dll 不同版本不同  具体目录根据自己的配置】
    AddType application/x-httpd-php .php .html .htm
    PHPIniDir c:/php                                 【配置PHP所在目录  具体目录根据自己的配置】  
    
    
    然后通过bin目录下   **httpd.exe  -t** 测试配置是否通过
    
    bin目录下 ApacheMonitor.exe 可以用来启动关闭 httpd服务
    
    
## 配置apache项目访问
### 1.配置项

        httpd.exe -k install -n "apache2.4"



        1.ServerName     配置域名访问地址
        例：ServerName 192.168.100.34:8080    ServerName www.baidu.com
        
        2.ServerRoot  配置apache安装目录
        
                 例     Define SRVROOT "c:/httpd"
                        ServerRoot "${SRVROOT}"   
                                 
        3.Listen      监听端口
        
                 例     Listen 8080
                 
        4.DocumentRoot    配置项目目录
        
                 例：  DocumentRoot "${SRVROOT}/htdocs/jnyy.iguanwei.com/public"
        
        5.<Directory 目录>
            权限
        </>Directory>
        配置目录权限相关
        
            例：
            <Directory "${SRVROOT}/htdocs/jnyy.iguanwei.com/public">
                        Options Indexes FollowSymLinks
                        AllowOverride All
                        Require all granted
             </Directory>
             
        6.配置默认访问文件
        
            <IfModule dir_module>
                  DirectoryIndex  index.php index.html
            </IfModule>     
         
        
### 伪静态

    1. httpd.conf配置文件 启动rewrite模块 如下：
    
        LoadModule rewrite_module modules/mod_rewrite.so


    2.项目根目录.htaccess文件配置入下  没有则新建
    
        <IfModule mod_rewrite.c>
         RewriteEngine on
         RewriteBase /
         RewriteCond %{REQUEST_FILENAME} !-d
         RewriteCond %{REQUEST_FILENAME} !-f
         RewriteRule ^(.*)$ index.php?s=/$1 [QSA,PT,L]
        </IfModule>
    
    
    3.对应apache  httpd.conf配置文件
    
         AllowOverride All

        <Directory 项目目录>
                                Options Indexes FollowSymLinks
                                AllowOverride All
                                Require all granted
        </Directory>
        
      
    
### 配置开机启动
        
       1.将apache加入windows服务   
       
            apache的bin目录下 执行 httpd.exe -thttpd.exe -k install -n "apache2.4"
       
       
       2.设置开机启动  然后再win+r 输入services.msc  打开后找到mysql 右键点击属性   将启动类型设置为自动即可
       

    
# apache相关知识
## windows下apache常用命令

    httpd -k install                           安装apache服务
    httpd -k uninstall                         移除apache服务
    
    httpd -k start                             启动apache服务
    httpd -k restart                           重启apache服务
    httpd -k stop                              关闭已安装的apache服务
    
    httpd -v                                   查看apache版本
    httpd -t                                   查看apache配置文件状态    
## 重写规则
    网站根目录下创建  .htaccess  文件
    
    1) R[=code](force redirect)  #强制外部重定向
    强制在替代字符串加上http://thishost[:thisport]/前缀重定向到外部的URL.如果code不指定，将用缺省的302 HTTP状态码。
    2) F(force URL to be forbidden)  #禁用URL,返回403HTTP状态码。
    3) G(force URL to be gone)  # 强制URL为GONE，返回410HTTP状态码。
    4) P(force proxy) # 强制使用代理转发。
    5) L(last rule) # 表明当前规则是最后一条规则，停止分析以后规则的重写。
    6) N(next round) # 重新从第一条规则开始运行重写过程。
    7) C(chained with next rule) # 与下一条规则关联
    如果规则匹配则正常处理，该标志无效，如果不匹配，那么下面所有关联的规则都跳过。
    8) T=MIME-type(force MIME type) # 强制MIME类型
    9) NS (used only if no internal sub-request) # 只用于不是内部子请求
    10) NC(no case) # 不区分大小写
    11) QSA(query string append) # 追加请求字符串
    12) NE(no URI escaping of output) # 不在输出转义特殊字符
    例如：RewriteRule /foo/(.*) /bar?arg=P1%3d$1 [R,NE] # 将能正确的将/foo/zoo转换成/bar?arg=P1=zoo
    13) PT(pass through to next handler) # 传递给下一个处理
    例如：
    RewriteRule ^/abc(.*) /def$1 [PT] # 将会交给/def规则处理
    Alias /def /ghi
    14) S=num(skip next rule(s)) # 跳过num条规则
    15) E=VAR:VAL(set environment variable) # 设置环境变量
    
    
## HTTP 跳转至 HTTPS 示例

    RewriteEngine on  # 开启重写
    RewriteCond %{SERVER_PORT} !^443$  # 如果访问的不是 443 端口
    RewriteCond %{REQUEST_URI} !^/tz.php  #如果访问的url 不是 /tz.php
    RewriteRule (.*) https://%{SERVER_NAME}/$1 [R]     # 跳转到  https：//域名+$1
    
    
    
    
## HTTP 80 强制转 HTTPS

    RewriteEngine On
    RewriteCond %{SERVER_PORT} 80  # 如果访问的是80 端口
    RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R,L]  # 跳转 https 
    
    
    
    
## 强制301重定向 HTTPS
  
    <IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteBase /  # 表示项目根目录
    RewriteCond %{SERVER_PORT} !^443$  # 如果访问的不是443 端口
    RewriteRule (.*) https://%{SERVER_NAME}/$1 [R=301,L]  # 重定向到 301
    </IfModule>
## 重命名目录
    
    RewriteRule   ^/?old_directory/([a-z\.]+)$   new_directory/$1   [R=301,L]
   
   
   
   
## 图片防盗链

   RewriteCond   %{HTTP_REFERER}   !^$ # 如果上个页面地址为空
   RewriteCond    %{HTTP_REFERER}   !^http://(www\.)?example\.com/   [NC]    #或者不是来自你自己的域名
   RewriteRule   \.(gif|jpg|png)$   - [F]  #禁止访问
   
   
   
## 如果文件不存在重定向到404页面

    RewriteCond   %{REQUEST_FILENAME}   !-f  # 如果访问的不是一个文件
    RewriteCond   %{REQUEST_FILENAME}   !-d  #如果访问的不是一个目录
    RewriteRule   .?   /404.php   [L]   #跳转到网站根目录
    # 也可携带参数
    #RewriteRule ^/?(.*)$ /404.php?url=$1 [L]
  

