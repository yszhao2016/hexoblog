---
title: shell脚本学习1
date: 2023-07-13 19:57:19
tags:
---

开头声明

    #!/bin/bash   表示使用bash作为解释器来执行shell脚本
   
    #!/bin/sh     表示使用默认的Unix shell解释器
    
    #!/usr/bin/perl表示使用Perl解释器

赋值

    语法：变量名=值
    webpath=/apps/web  等号必须紧挨变量 不然会报错！
    
取变量值
    
    语法：$变量名  
    例：$webpath
    
取命令执行的值

       语法：$(命令) 
       例：
            test=$(ls /apps/web)
            projectnames=$(ls $webpath)
            
遍历

    语法：   for  值 in 数组
             do
                执行代码
             done    
             
     例：
        for projectname in $projectnames
        do
            echo $projectname
        done          
        
        
逻辑判断
    
    语法
        if [ 判断条件]
        then
           执行代码
        fi   

        if [ 判断条件];then
            执行代码
        fi
        
        
        
    if [ command ];then
         符合该条件执行的语句
    elif [ command ];then
         符合该条件执行的语句
    else
         符合该条件执行的语句
    fi


if文件判断
 
     -e：判断文件或目录是否存在；
     
     -d：判断是否为目录以及是否存在；
     
     -f：判断是否为普通文件以及是否存在；
     
     -r：判断是否有读权限；
     
     -w：判断是否有写权限；
     
     -x：判断是否有执行权限     
     
if字符串判断
    
    [ INT1 -eq INT2 ] INT1和INT2两数相等返回为真 ,=
    [ INT1 -ne INT2 ] INT1和INT2两数不等返回为真 ,<>
    [ INT1 -gt INT2 ] INT1大于INT2返回为真 ,>
    [ INT1 -ge INT2 ] INT1大于等于INT2返回为真,>=
    [ INT1 -lt INT2 ] INT1小于INT2返回为真 ,<
    [ INT1 -le INT2 ] INT1小于等于INT2返回为真,<=

if数值判断
    
    [ ! EXPR ] 逻辑非，如果 EXPR 是false则返回为真。
    [ EXPR1 -a EXPR2 ] 逻辑与，如果 EXPR1 and EXPR2 全真则返回为真。
    [ EXPR1 -o EXPR2 ] 逻辑或，如果 EXPR1 或者 EXPR2 为真则返回为真。
    [ ] || [ ] 用OR来合并两个条件
    [ ] && [ ] 用AND来合并两个条件

if判断示例

    if [ -f $replacefile1 ] && [ ! -f $replacefile1".bak" ]
    then
        执行代码
    fi
                 
                 
    if [ "$(whoami)" != 'root' ]; then
       echo  "You  have no permission to run $0 as non-root user."
       exit  1;
    fi             