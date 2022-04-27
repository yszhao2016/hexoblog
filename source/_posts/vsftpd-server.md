---
title: 通过vsftpd搭建ftp服务器
date: 2022-04-15 22:12:12
categories: # 分类
- server
tags:
---

1.yum 安装

    yum install vsftpd


2.编译安装

    yum -y install epel-release && yum -y install pam pam-devel db4-utils
    wget https://security.appspot.com/downloads/vsftpd-3.0.5.tar.gz

    tar xf vsftpd-3.0.3.tar.gz
    
    make clean && make -j 4 && make install


    如果编译的时候报错
    /usr/bin/ld: cannot find -lcap
    查找该 .so 文件
    find / -name "*libcap.so*"
    /usr/lib64/libcap.so.2.22
    /usr/lib64/libcap.so.2
    ln -sv /usr/lib64/libcap.so.2 /usr/lib64/libcap.so

3. /etc/vsftpd/vsftp.conf  配置文件
```
    #关闭匿名访问
    anonymous_enable=NO    
    local_enable=YES
    #启用虚拟账户
    guest_enable=YES
    ##把虚拟账户映射到系统账户virftp
    guest_username=virftp
    ##使用虚拟用户验证（PAM验证）
    pam_service_name=vsftpd
    #设置存放各虚拟用户配置文件的目录（此目录下与虚拟用户名相同的文件为他的配置文件）
    user_config_dir=/etc/vsftpd/vsftpd_viruser
    ##启用chroot时，虚拟用户根目录允许写入
    allow_writeable_chroot=YES
    
    # Uncomment this to enable any form of FTP write command.
    write_enable=YES
    #
    # Default umask for local users is 077. You may wish to change this to 022,
    # if your users expect that (022 is used by most other ftpd's)
    local_umask=022
    listen_port=10021
    ftp_data_port=10020
    
    #被动模式 配置
    pasv_enable=YES
    pasv_min_port=20000
    pasv_max_port=2001Z 
    # 可以发送消息当访问某个目录时
    dirmessage_enable=YES
    # 开启上传下载记录
    xferlog_enable=YES
    # 日志标准输出
    xferlog_std_format=YES
    
    connect_from_port_20=YES
    #监听ipv4
    listen=NO
    # 监听IPv6和监听IPv4
    listen_ipv6=YES

    userlist_enable=YES
    # 访问控制
    tcp_wrappers=YES
```





