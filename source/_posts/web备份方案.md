---
title: web备份方案
date: 2021-12-23 20:33:32
tags:
---


一、服务器环境

        192.168.100.230 web服务器
        192.168.100.20  备份服务器 （rsync服务端）

二、备份服务器安装 rsync 服务端

        rpm -qa|grep rsync    //检查是否安装 rsync
        
        yum install -y rsync
        
        vim /etc/rsyncd.conf   //编辑rsyncd服务
        
        #rsyncd.conf start
        uid = rsync                                
        gid = rsync
        use chroot=no
        max connections=200
        timeout = 300
        pid file = /var/run/rsyncd.pid
        lock file = /var/run/rsyncd.lock
        log file = /var/log/rsyncd.log
        ignore errors
        read only = false
        list = false 
        hosts allow = 172.16.1.0/24
        #hosts deny = 0.0.0.0/32
        auth users = rsync_backup
        secrets file = /etc/rsync.password
        fake super = yes
        [backup]
        comment = "backup dir by oldboy"
        path = /home/backup  #备份的目录
        
        
        创建rsync 服务管理用户
        useradd -s /sbin/nologin -M rsync
        
        创建数据备份存储目录
        chown -R rsyn.rsync /home/backup
        
        创建认证用户密码文件
        echo "rsync_backup:admin.123!@#$"  >/etc/rsync.password
        chmod 600 /etc/rsync.password
        
        启动rsync服务
        systemctl start rsyncd



二、配置web 服务器端（192.168.100.230）

        确认 rsync是否安装
            rpm -qa|grep rsync
        
        建立认证文件
        
            echo "密码字符串（与rsync一致）"  >/etc/rsync.password
            chmod 600 /etc/rsync.password
        
        web01主机数据传输到backup主机测试测试传输
        
            Push: rsync [OPTION...] SRC... [USER@]HOST::DEST
            交互式：rsync -avz /etc/hosts  rsync_backup@172.16.1.41::backup
            非交互式：rsync -avz /etc/hosts  rsync_backup@172.16.1.41::backup --password-file=/etc/rsync.password
            
            
         shell 脚本   
            
            #!/bin/bash
            objectFilename=('文件夹名称');
            sourcePath='/apps/web/';
            for(( i=0;i<${#objectFilename[@]};i++)) 
            do 
                    tar zcf /home/backup/web/source/${objectFilename[i]}-$(date +%Y-%m-%d).tar.gz -C ${sourcePath} ${objectFilename[i]}; 
                    rsync -avz /home/backup/web/source/  rsync_backup@192.168.100.20::backup --password-file=/etc/rsync.password
                    rm -rf /home/backup/web/source/${objectFilename[i]}-$(date +%Y-%m-%d).tar.gz
            done;
