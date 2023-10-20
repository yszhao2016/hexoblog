---
title: mysql的常用应用
date: 2023-10-17 10:12:16
tags:
---


#mysql忘记密码,重置密码


1.修改my.cnf

添加

    --skip-grant-tables

2.进入数据

    执行
    use mysql;
    mysql> update user set password=password('123') where user='root' and host='localhost';
    mysql> flush privileges;



#mysql导出、导入数据命令

导出
mysqldump -u <用户名> -p<密码> <数据库名> > <输出文件路径>

示例

     mysqldump  -u root -p wastewater.iguanwei.com >bak.sql


导入

    方法一：
    mysql -u用户名 -p 数据库名 < 123.sql
    方法二：
    source 123.sql;






#