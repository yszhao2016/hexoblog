---
title: 搭建 mysql_server
categories:
  - server
abbrlink: ca8fa022
date: 2022-04-17 22:53:37
tags:
---
# centos mysql安装

1.网站：https://downloads.mysql.com/archives/community/

2.mysql官网找到相应版本安装包

3.然后下载本地 tar -xvf mysql-5.7.21-1.el7.x86_64.rpm-bundle.tar

4.rpm -qa|grep mariadb 查看是否安装mariadb数据库，有就卸载  yum remove 包名 <font color='red'>不卸载干净，会报错哦</font>

5.按下列包顺序安装
    
    rpm -ivh mysql-community-common-5.7.19-1.el7.x86_64.rpm
    
    rpm -ivh mysql-community-libs-5.7.19-1.el7.x86_64.rpm
    
    rpm -ivh mysql-community-devel-5.7.19-1.el7.x86_64.rpm
    
    rpm -ivh mysql-community-libs-compat-5.7.19-1.el7.x86_64.rpm
    
    rpm -ivh mysql-community-client-5.7.19-1.el7.x86_64.rpm

    rpm -ivh mysql-community-server-5.7.19-1.el7.x86_64.rpm
    
6.优化配置   

  6.1 /data/mysql/logs/相关目录需要创建
    
    mkdir -p  /data/mysql/logs/
  
  6.2  my.cnf 配置文件
  
    port=3306
    设置端口号
    
    datadir=/data/mysqldata
    #数据存在目录
        
    ############## Slow Log   ######################
    slow_query_log=ON
    # 开启慢查询日志
    
    slow_query_log_file=/data/mysql/logs/slow.log
    # # 慢查询日志存放路径
    
    long_query_time=10
    # # 超过10秒的查询，记录到慢查询日志，默认值10
    
    log_queries_not_using_indexes=ON
    # # 没有使用索引的查询，记录到慢查询日志，可能引起慢查询日志快速增长
    
    log_slow_admin_statements=ON
    # # 执行缓慢的管理语句，记录到慢查询日志
    # # 例如 ALTER TABLE, ANALYZE TABLE, CHECK TABLE, CREATE INDEX, DROP INDEX, OPTIMIZE TABLE, and REPAIR TABLE.
    
    
    ###################     Error Log   ####################
    log_error=/data/mysql/logs/mysqld.log
    ## 错误日志存放路径
    log_error_verbosity = 2
    ## 全局动态变量，默认3，范围：1～3
    
    
    ###################     Bin Log    ######################
    server_id = 6
    ###################     Error Log   ####################
    log_error=/data/mysql/logs/mysqld.log    
    
7.  启动 systemctl start mysqld
    安装完毕 初始密码一般在/var/log/mysqld.log文件中   
    cat   /var/log/mysqld.log |grep password

8.登录数据库 修改密码 mysql -u root -p


9.登录后修改密码 

    SET PASSWORD = PASSWORD('xxx@520Flzx3qc');
    
    允许远程root用户连接
    GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_password' WITH GRANT OPTION;
    FLUSH PRIVILEGES;
    

10.相关命令
    
    开机启动
    systemctl enable mysqld
    
    systemctl stop mysqld
    systemctl start mysqld
    systemctl restart mysqld
    
 
#安装报错解决

    Error: 
     Problem: cannot install the best candidate for the job
      - nothing provides libcrypto.so.10()(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libssl.so.10()(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(libcrypto.so.10)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libssl.so.10(libssl.so.10)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(OPENSSL_1.0.1_EC)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
      - nothing provides libcrypto.so.10(OPENSSL_1.0.2)(64bit) needed by php73-php-fpm-7.3.33-10.el7.remi.x86_64
    
    
    
    
    解决方案

    wget https://rpmfind.net/linux/centos/8-stream/AppStream/x86_64/os/Packages/compat-openssl10-1.0.2o-4.el8.x86_64.rpm
    yum install compat-openssl10
    或者
    yum install http://mirror.centos.org/centos/8-stream/AppStream/x86_64/os/Packages/compat-openssl10-1.0.2o-3.el8.x86_64.rpm
    或者
    yum install -y ./compat-openssl10-1.0.2o-4.el8.x86_64.rpm
    
    
    
    [root@ecs-41618143 ~]# rpm -ivh mysql-community-client-5.7.34-1.el7.x86_64.rpm 
    warning: mysql-community-client-5.7.34-1.el7.x86_64.rpm: Header V3 DSA/SHA1 Signature, key ID 5072e1f5: NOKEY
    error: Failed dependencies:
    	libncurses.so.5()(64bit) is needed by mysql-community-client-5.7.34-1.el7.x86_64
    	libtinfo.so.5()(64bit) is needed by mysql-community-client-5.7.34-1.el7.x86_64
    	
    	
     解决方案	
     
     yum install libncurses*
