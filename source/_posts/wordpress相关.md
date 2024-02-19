---
title: wordpress相关
abbrlink: 4aea2b73
categories:
  - PHP开发
date: 2023-11-01 11:01:22
tags:
---



# wordpress 迁移后还是访问之前的域名
    
    1.找到 前缀_options 表修改 siteurl  home字段 
    2.或者登录后台修改




# wordpress 迁移后栏目打开是404

    nginx加上如下配置
    
    location / {
     try_files $uri $uri/ /index.php?$args;
    }
    # Add trailing slash to */wp-admin requests.
    rewrite /wp-admin$ $scheme://$host$uri/ permanent;
    
    
    
#  修改忘记密码 修改
    
    修改 前缀_users 表    user_pass 字段 
    MD5 加密


    