---
title: mysql-主从数据库
abbrlink: 585d050b
date: 2024-07-23 10:33:22
categories:
 - mysql
tags:
 - mysql
---


# mysql主从的应用


## 用来数据迁移

    开启主从同步，将数据导入主节点，让从节点同步；
    
    将数据分别导入主从节点，让主从节点数据一致后，建立连接，开启同步
    
## 数据备份


##  读写分离
    
    
# 配置

## 主节点配置

### 主节点 my.cnf配置
    my.cnf  mysqld    
    
    # 开启日志
    general_log = 1
    general_log_file = mysql.log
    # 主从复制配置
    server-id = 1
    # binlog日志格式，mysql默认采用statement，建议使用mixed
    # STATEMENT模式（SBR）
    # 每一条会修改数据的sql语句会记录到binlog中。优点是并不需要记录每一条sql语句和每一行的数据变化，减少了binlog日志量，节约IO，提高性能。缺点是在某些情况下会导致master-slave中的数据不一致(如sleep()函数， last_insert_id()，以及user-defined functions(udf)等会出现问题)
    # ROW模式（RBR）
    # 不记录每条sql语句的上下文信息，仅需记录哪条数据被修改了，修改成什么样了。而且不会出现某些特定情况下的存储过程、或function、或trigger的调用和触发无法被正确复制的问题。缺点是会产生大量的日志，尤其是alter table的时候会让日志暴涨。
    # MIXED模式（MBR）
    # 以上两种模式的混合使用，一般的复制使用STATEMENT模式保存binlog，对于STATEMENT模式无法复制的操作使用ROW模式保存binlog，MySQL会根据执行的SQL语句选择日志保存方式。
    binlog_format= MIXED
    # binlog日志文件
    log-bin=mysql-bin
    # 是否只读，1 代表只读，0代表读写，主数据库需要读写，设置0
    read-only=0
    # binlog过期清理时间
    expire_logs_days=7
    # binlog每个日志文件大小
    max_binlog_size= 100m
    # binlog缓存大小
    binlog_cache_size= 4m
    # 最大binlog缓存大小
    max_binlog_cache_size= 512m
### 相关sql操作

    ## 创建dd用户 授权只能ip 192.168.227.134 访问 密码设置为123456
    CREATE USER 'dd'@'192.168.227.134' IDENTIFIED BY '123456';
    
    ## 设置一个从服务器（slave）来复制主服务器（master）的数据权限
    GRANT REPLICATION SLAVE ON *.* TO 'dd'@'192.168.227.134';

    show master status 
    
    
### 从节点 my.cnf配置    

    server-id=2
    #它指定了从服务器（slave）用于存储从中继日志（relay logs）
    #中读取并准备应用于本地数据库的事件的文件的路径和名称模式。
    relay-log=mysql-relay-bin
    
    
    sql
    //配置并启动主从复制
    change master to
        -> master_host='192.168.227.133',		#主的ip地址
        -> master_user='dd',					#创建的用户名
        -> master_password='123456',			#创建的密码
        -> master_log_file='mysql-bin.000001',	#日志文件
        -> master_log_pos=328;					#数据的最新位置

    //开启奴隶模式
    start slave;	
    
    show slave status 