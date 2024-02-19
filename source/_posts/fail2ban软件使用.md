---
title: fail2ban软件使用
abbrlink: f44631de
categories:
  - 服务器相关
date: 2023-07-09 14:19:39
tags:
---
# 简介
    
    CentOS 中使用fail2ban和firewalld限制IP拦截cc攻击
    
# 安装

    yum -y install fail2ban
    
    
# 新建规则
    
    /etc/fail2ban/filter.d/  编写过滤规则的目录 

    vi /etc/fail2ban/filter.d/nginx-cc.conf
    
    [Definition]
    #failregex = <HOST>-.*-.*HTTP/1.*(404|301|503).*$
    failregex = ^<HOST> -.*"(GET|POST).* HTTP\/.*$
    ignoreregex =

# 正则规则检查

    fail2ban-regex /var/log/nginx/access.log "<HOST> -.*- .*HTTP/1.* .* .*$"  
    
# 根据配置文件检查

    fail2ban-regex /var/log/nginx/access.log /etc/fail2ban/filter.d/nginx-cc.conf  



# 控制规则配置文件

    [DEFAULT]
    ignoreip = 127.0.0.1/8  117.89.130.84 117.89.134.183/16  218.2.244.241/16 49.82.132.215/16 122.195.252.237/16 114.238.64.157/16 180.110.114.152/16
    #放行的ip 可以带网段
    ignorecommand =
    bantime  = 60
    #屏蔽时间，-1是永久屏蔽
    findtime  = 60
    #设置限定时间内超过 最大尝试次数 限制次数即被封锁
    maxretry = 5
    #最大尝试次数
    backend = auto
    #日志修改后检测机制
    usedns = warn
    #ssh链接加速
    logencoding = auto
    enabled = false
    [sshd]
    ## 分类设置（基于 SSHD 服务的防护） 
    enabled = true
    ## 是否开启防护，false 为关闭 
    filter = sshd
    ## 过滤规则 filter 名称，对应 filter.d 目录下的 sshd.conf  action   = iptables[name=SSH, port=22, protocol=tcp] 
    #《###action = iptables[name=SSH, port=ssh, protocol=tcp] ## 动作参数
    #sendmail-whois[name=SSH, dest=you@example.com, sender=fail2ban@example.com,   sendername="Fail2Ban"]
    ## 邮件通知参数
    ## 收件人地址           ## 发件人地址####》   这是个例子 
    logpath = /var/log/secure
    ## 检测系统登陆日志文件 
    maxretry = 3
    ## 最大尝试次数
    banaction=iptables-allports
    bantime = 3600
    [nginx-cc] 
    #过滤规则名称 下方就是 规律规则 相关 限制条件
    enabled = true
    port=http,https
    filter = nginx-cc
    findtime = 1
    #1秒钟检查一次
    maxretry = 100
    #最多100个
    bantime = 3600
    #封锁ip的时间
    banaction=iptables-multiport
    #匹配的哪些日志文件
    logpath = /var/log/nginx/*access.log
              /var/log/nginx/access.log



# 常用命令

    #启动
    systemctl start fail2ban
    #停止
    systemctl stop fail2ban
    
    #查看具体规则限制了哪些ip
    fail2ban-client status 规则名称
    示例：   fail2ban-client status nginx-cc
    
    #解封某条过滤规则封锁的ip
    fail2ban-client set nginx-cc unbanip  112.84.237.102
    