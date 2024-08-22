---
title: linux-升级openssh
abbrlink: 59b7aac2
date: 2023-08-01 19:48:14
tags:
---


# centos系统
    
## 查看是否安装openssh    

    rpm -qa|grep openssh
    有则卸载
    
    卸载用rpm -e 如果出现依赖包导致无法卸载，在最后面加上--nodeps即可
    rpm -e openssl --nodeps
    yum remove openssh-server
    
    卸载还可以手动删除
      /etc/init.d/sshd 
      /etc/ssh
      /usr/bin/ssh
      /usr/sbin/sshd
          
## 查当前openssh版本是否需要升级
     
    ssh -V

## 备份配置文件

    mv /etc/ssh /etc/ssh.bak
    
## 下载最新版本
    
    https://www.openssh.com/openbsd.html    
    
    
## 编译安装

    ./configure \
      --prefix=/usr \
      --sysconfdir=/etc/ssh \
      --with-md5-passwords \
      --with-pam \
      --with-tcp-wrappers \
      --with-ssl-dir=/usr/local/ssl
      
      
      make && make install
        
## 修改相关配置

    cp -p contrib/redhat/sshd.init /etc/init.d/sshd 
    
    chmod u+x /etc/init.d/sshd 
    
    /etc/init.d/sshd start 此命令后就加入systemctl
    
    systemctl enable sshd 加入开机启动
    
    # vim /etc/ssh/sshd_config
    PermitRootLogin yes                #允许root账户登录，单root账户必须加上，其他的参数请自行修改
    
 ## 报错处理
 
    1.编译时报错
    
        configure: error: PAM headers not found
        
    解决方法
        
        yum -y install pam-devel
        
        
    2.编译别的目录，然后通过相应文件覆盖或者软连接方式更新
      如果密码正确 就是登录不上
      
      删除重装     


# ubutun系统

## 查看是否安装

    dpkg --list|grep ssh
    卸载
    sudo apt-get remove openssh-server 
    
## 编译过程参考centos即可    