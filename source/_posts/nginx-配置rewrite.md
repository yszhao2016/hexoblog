---
title: nginx-配置rewrite
abbrlink: a0181459
categories:
  - 服务器相关
  - nginx
date: 2024-06-29 12:04:32
tags:
---


# rewrite 作用

用于实现URL的重写，URL的重写是非常有用的功能，
比如它可以在我们改变网站结构之后，不需要客户端修改原来的书签，
也无需其他网站修改我们的链接，就可以设置为自动访问，另外还可以在一定程度上提高网站的安全性

示例  
浏览器访问url http://www.example.com/abc/file/a.jpg 
实际访问 http://www.example.com/bb/a.jpg

浏览器访问url http://www.example.com/index/abc.html
实际访问 http://www.example.com/index.php?r=index/abc



# rewrite应用场景

1、URL看起来更规范、合理
2、企业会将动态URL地址伪装成静态地址提供服务
3、网址换新域名后，让旧的访问跳转到新的域名上
4、服务端某些业务调整


# rewrite调试
rewrite 日志记录指令
指令	rewrite_log 
作用域 http, server, location     
指令值选项	on 或 off
当指令值为 on 时，rewrite 的执行结果会以 notice 级别记录到 Nginx 的 error 日志文件中

示例：
```nginx
  http{
        rewrite_log on;
  }
```
  


# nginx正则表达式


常用的正则表达式元字符
^：匹配输入字符串的起始位置
$：匹配输入字符串的结束位置
*****：匹配前面的字符零次或多次
+：匹配前面的字符一次或多次
?：匹配前面的字符零次或一次
.：匹配除\n之外的任何单个字符 使用[.\n]可以匹配包括\n在内的任意字符
****：转义符
\d：匹配纯数字
{n}：重复n次
{n,}：重复n次或更多次
[c]：匹配单个字符c
[a-z]：匹配a-z小写字母的任意一个
[a-zA-Z]：匹配a-z小写字母或A-Z大写字母的任意一个
（）：表达式的开始和结束位置
|：或运算符




# 实际应用


    location ~* .*\.(mp4|avi|wmv|mpg|flv|rm|mov) {
      
      add_header Accept-Ranges bytes;
      if ($request_uri ~* ^(.*)file/Image/annex/(.*)/(.*)){
        set $file_name $3;
        rewrite (.*) $scheme://$host/$file_name;
      }
      #视频保存的目录
      root /www/wwwroot/tjproject-resources/WebAnnexFile/451772413496005765/;
      mp4;
      mp4_buffer_size 1m;#处理mp4初始内存大小
      mp4_max_buffer_size 50m;#处理mp4最大内存大小
    }
    
    
    location ~ .*\.(mp4|avi|wmv|mpg|flv|rm|mov) {
      if ($request_uri ~* ^/monitor_videos/(.*)){
        set $file_name $1;
        rewrite (.*) $scheme://$host/$file_name;
      }
      # #视频保存的目录
      root /www/wwwroot/aigis_resources_yzc/monitor_videos/;
      mp4;
      mp4_buffer_size 1m;#处理mp4初始内存大小
      mp4_max_buffer_size 50m;#处理mp4最大内存大小
    }
    
    
    location ~* /upload/。*\.php${
    
        rewrite (.+) http://www.index.com permanent
    }
    
    
    server { 
        listen 80; 
        server_name example.com www.example.com; 
            if ($server_port !~ 443){
             rewrite ^(/.*)$ https://$host$1 permanent; 
            } 
            # 其他配置... 
    }
    
    
    if ($scheme = http ) {
        return 301 https://$host$request_uri;
    }