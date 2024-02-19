---
title: 通过vsftpd搭建ftp服务器
categories:
  - 服务器相关
abbrlink: 40a34212
date: 2022-04-15 22:12:12
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
    
    
    
3./etc/vsftpd/vsftp.conf  配置文件    

    配置文件  vsftp.conf 

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
    #监听端口 默认21
    listen_port=10021
    #数据传输端口 默认20
    ftp_data_port=10020
    
    #被动模式 配置
    pasv_enable=YES
    pasv_min_port=20000
    pasv_max_port=20010
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
**注意 根据需求自行修改<b><i>vsftp.conf</i></b>端口号以及模式：**
<font color="#FF0000">#监听端口 默认21<br/>listen_port=10021<br/>#数据传输端口 默认20<br/>ftp_data_port=10020<br/>#被动模式 配置<br/>pasv_enable=YES<br/>pasv_min_port=20000<br/> pasv_max_port=20010<br/></font> 




4、生成虚拟用户数据库：

    touch /etc/vsftpd/vir_user
    vir_user文件内容：
        user1
        password1
        user2
        password2

    yum -y install libdb-utils
    db_load -T -t hash -f /etc/vsftpd/vir_user /etc/vsftpd/vir_user.db
    chmod 700 /etc/vsftpd/vir_user.db


5、将auth及account的所有配置行均注释掉，添加如下两行
```
vim /etc/pam.d/vsftpd

auth                 required     pam_userdb.so   db=/etc/vsftpd/vir_user 
account              required     pam_userdb.so   db=/etc/vsftpd/vir_user
```

6、增加一个系统用户
```
mkdir /home/ftproot
useradd -d /home/ftproot -s /sbin/nologin virftp
chown -R virftp:virftp /home/ftproot
```

7、创建和配置虚拟用户各自的配置文件，文件名称是‘虚拟用户名
```
vim /etc/vsftpd/vsftpd_viruser/根据创建虚拟用户密码文件来(user1,user2)


#允许写入
write_enable=YES
#允许浏览FTP目录和下载
anon_world_readable_only=NO
#允许虚拟用户上传文件
anon_upload_enable=YES
#允许虚拟用户创建目录
anon_mkdir_write_enable=YES
#允许虚拟用户执行其他操作（如改名、删除）
anon_other_write_enable=YES
#上传文件的掩码,如022时，上传目录权限为755,文件权限为644
anon_umask=022
#指定虚拟用户的虚拟目录（虚拟用户登录后的主目录） 可以指定其他目录  注意权限（给虚拟账号权限chown -R virftp:virftp 目录）
local_root=/ftproot/admin/

```

8、各配置文件说明：

    /etc/vsftpd/vsftpd.conf                             ：vsftpd的主配置文件
    /etc/vsftpd/vir_user                                   ：虚拟用户的账号密码文件  
    /etc/vsftpd/vsftpd_viruser/user1            ：虚拟用户‘user1’的配置文件
    /etc/pam.d/vsftpd                                     ：启用虚拟用户验证功能的配置文件
  


9、命令

    rename [filename]  [newfilename]，重命名远程Linux FTP服务器上指定的文件｡
    rename 1111.xlsx   processed/1111.xlsx
    
    