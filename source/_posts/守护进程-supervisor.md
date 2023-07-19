---
title: 守护进程-supervisor
date: 2023-07-19 11:38:09
tags:
---

# 一、安装

    yum install supervisor    #centos 系统
    
    apt install supervisor    #ubuntu 系统
    
    
# 二、配置守护进程   

<font color='red'>在/etc/supervisord.d/下新建后缀名为ini的文件</font>

    ; 设置进程的名称，使用 supervisorctl 来管理进程时需要使用该进程名
    [program:myblog] 
    directory = /home/web/myblog          ; 程序的启动目录
    command=./start.sh start              ; 启动命令 最好绝对路径
    autostart = true                      ; 在 supervisord 启动的时候也自动启动
    numprocs=1                            ; 默认为1
    RestartSec=30
    process_name=%(program_name)s         ; 默认为 %(program_name)s，即 [program:x] 中的 x
    user=root                             ; 使用 root 用户来启动该进程
    autorestart=true                      ; 程序崩溃时自动重启，重启次数是有限制的，默认为3次
    redirect_stderr=true                  ; 重定向输出的日志
    stderr_logfile= /home/web/myblog/myblog_stderr.log
    stdout_logfile = /home/web/myblog/myblog_stdout.log
    loglevel=info
    
# 三、supervisord管理

    systemctl status  supervisord
    systemctl start  supervisord  
    systemctl stop  supervisord  
    systemctl restart  supervisord  
   
# 四、supervisorctl管理    
    
    supervisorctl start server:myblog