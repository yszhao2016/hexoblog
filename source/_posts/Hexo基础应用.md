---
title: Hexo基础应用
date: 2021-12-10 14:20:38
tags:
---

1.首先安装node,具体参考https://hexo.io/zh-cn/docs/

2.初始化 项目
``` bash
hexo init blog
```
将会在当前目录下产生blog文件夹

3.新建文章
``` bash
hexo n 文章标题  //完整命令 hexo new 文章标题
```
相应会在source/_posts 目录下产生   文章标题.md

4.生成html
``` bash
hexo g        //完整命令  hexo generate
```
将会在public目录生成相应的html文件

5.开启本地测试环境
``` bash
hexo s        //完整命令  hexo server
```
    http://loaclhost:4000就可以访问了


6.发布代码到github.io
    
    配置修改_config.yml文件
```
deploy:
  type: 'git'
  repo: https://github.com/github账号名称/github账号名称.github.io
  branch: master
```
``` bash
hexo d       //发布到线上  hexo generate
```







