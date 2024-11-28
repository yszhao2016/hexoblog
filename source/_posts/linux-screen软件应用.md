---
title: linux-screen软件应用
abbrlink: 87fcad5d
date: 2024-11-25 19:00:26
tags:
  - linux
categories:
  - 服务器相关  
---


#screen 简介

screen被称之为一个全屏窗口管理器, 用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。
注意有会话(session)和窗口(window)两个概念。 进入screen会话后，可在会话中创建多个窗口（window）




# 相关使用命令


方式一: 创建有名字的screen任务

screen -S {task_name}

方式二: 创建没有名字的screen任务

screen



查看screen任务
执行-ls 可以查看到系统中所有的screen任务的信息（如pid）

screen -ls


将screen任务放到后台
快捷键 ctrl+a+d     此时,程序仍在后台执行;

另外一种方式：再打开一个终端

screen -d {pid}
或者
screen -d {task_name}



进入screen任务
screen -r {pid}
或者
screen -r {task_name}



暂离当前screen:
这个也是screen命令的精髓，用组合键Ctrl+a+d 就能detached当前的screen，回到默认界面。



杀死当前窗口：
使用Ctrl+a+k命令


关闭当前窗口：
这个可以直接用Ctrl+d 或者exit 来关闭，当这个窗口是现有screen的最后一个窗口时，
就会触发上面讲的关闭当前screen的事件了。