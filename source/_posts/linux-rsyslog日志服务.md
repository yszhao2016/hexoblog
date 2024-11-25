---
title: rsyslog日志上报服务
abbrlink: aaedf8b3
categories:
  - 服务器相关
date: 2024-02-01 18:52:54
tags:
  - linux  
---

# rsyslog 基础
## 日志类别

    日志类型 日志内容
    auth 用户认证时产生的日志
    syslog 系统日志
    local0~local7 自定义程序使用
    等等

## Properties模板元素属性

常用的变量

    hostname 打印该日志的主机名。
    fromhost 接收的信息来自于哪个节点。这里是DNS解析的名字。
    fromhost-ip 接收的信息来自于哪个节点，这里是IP，本地的是127.0.0.1。
    syslogtag 信息标签。大致形如 programed[14321] 。
    programname 文件名
    
    Properties与时间相关的属性属性 释义
    $now 当前日期时间戳，格式为YYYY-MM-DD (2020-07-08)
    $year 当前年份， 四位数 (2020)
    $month 当前月份， 两位数 (07)
    $day 当前月份的日期，两位数 (08)
    $wday 当前天数周几 ：0=Sunday,...6=Saturday
    $hour 当前小时（24小时机制），两位数(16)
    $hhour 半小时机值，就是0-29分钟显示0，30-59分钟显示1。
    $qhour 一刻钟机值，通过0-3显示，每15分钟一截。
    $minute 当前分钟数，两位数(57)

示例
    定义日志目录
    
    $template RemoteTestlogs,"/var/log/syslog/%fromhost-ip%/test.log"
    
    /var/log/syslog/%$YEAR%-%$MONTH%-%$DAY%/%FROMHOST-IP%-%programname%.log
    
##日志文件权限 

日志文件权限新生成的目录和文件权限可以在配置文件设置

     vim /etc/rsyslog.conf
     #尽量在开头设置
     $FileOwner root
     $FileGroup root
     $FileCreateMode 0755 #文件权限
     $DirCreateMode 0755 #目录权限
     $Umask 0022
     
## 模版配置


 1、示例1
 
     $template DynamicFile,"/var/log/test_logs/%timegenerated%-test.log"
     *.* ?DynamicFile
 
 2、示例2
 
     $template RemoteHost,"/home/rsyslog-data/%$YEAR%-%$MONTH%-%$DAY%/%FROMHOST-IP%.log
     *.* ?RemoteHost
     #    & ~ 忽略所有的日志.
     & ~
 
 3、示例3
 
     #配置文件存储模板
     $template RemoteAuditLogs,"/data/auditd/%FROMHOST-IP%/audit.log" *
     $template RemoteNetworkLogs,"/data/network-log/%HOSTNAME%/network.log" *
     #引用模板
     local6.* ?RemoteNetworkLogs
     local2.* ?RemoteAuditLog   
# 配置案例
## 客户端配置

    
1.2 相关配置
    
 增加imfile模块配置，【不然自定义的日志，无法发送到服务端】
       
    $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
    $ModLoad imjournal # provides access to the systemd journal
    # 下发添加 方法一
    module(load="imfile" PollingInterval="1") 
    # 方法二
    $ModLoad imfile
    
    local7.* /var/log/local7.log
    
    #只发送local7 类型日志
    #local7.* @@121.4.27.111
    #将所有日志 多发送  
    *.* @@121.4.27.111
    
    
 新增/etc/rsyslog.d/local7.conf
 
 
      #工作目录 这个可不配 默认/var/log/下
      #$WorkDirectory /var/log/rsyslog
      
      #输入文件模式
      input(type="imfile"
           File="/var/log/local7.log"   #可指定任意文件，但需要将文件权限置为777
           Tag="local7"      #文件标识，服务器接收到消息可以使用这个tag分类
           Severity="info"            #日志级别
           Facility="local7"             
           PersistStateInterval="1"     #回写偏移量数据到文件间隔时间(秒)
           ruleset="remote")          #规则集，rsyslog.conf中定义的rule名称

## 服务端配置

定义开启 接收的协议 以及端口
    
    # Provides UDP syslog reception
    $ModLoad imudp
    $UDPServerRun 514
     
    # Provides TCP syslog reception
    $ModLoad imtcp
    $InputTCPServerRun 514

定义模版

   1.示例1
   
    #生成模板
    $template SpiceTmpl,"%msg:2:$%\n"
    $template CatalinaDynaFile,"/var/log/rsyslog/%fromhost-ip%/catalina_%$YEAR%-%$MONTH%-%$DAY%.log"
      
    #匹配规则，文章后面将分享其他配置类型
    :fromhost-ip,contains,"192.168.88.132" ?CatalinaDynaFile;SpiceTmpl
    
   2.示例2
    
    $template RemoteLogs,"/var/log/syslog/%$YEAR%-%$MONTH%-%$DAY%/%FROMHOST-IP%-%programname%.log"
    *.* ?RemoteLogs
    & ~

## 测试
    
    -it error：这是两个选项的组合。-i选项指定附加进程ID（PID）到日志消息中，-t error选项指定将"error"作为消息的标记（TAG）。
    -p local5.crit：这是指定日志消息的优先级的选项。在这个例子中，local5.crit表示使用syslog的local5设施和crit（严重）优先级。
    "hello world"：这是要记录的实际消息内容。
    
    该命令用于向系统日志中添加一条日志消息
    logger -it local1 -p local1.info "hello"
  
