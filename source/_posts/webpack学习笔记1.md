---
title: webpack学习笔记1
abbrlink: 30bc514d
categories:
  - 前端
  - webpack
date: 2022-01-01 15:19:05
tags:
---

#1. webpack 安装

    全局安装 nodejs
        验证  node -v
              npm -v
    npm install webpack -g
    npm install webpack-cli -g
    正常情况下 安装webpack-cli 会自动安装webpack
    npm uninstall webpack -g 全局卸载

    单独项目内安装
    npm install webpack webpack-cli --save-dev
    或者
    npm install webpack webpack-cli -D

    运行npx webpack -v   //注 npx 会在node_module文件夹中找
    
    安装特定版本
    npm info webpack   //查看webpack有那些版本
    npm install webpack@4.16.5 webpack-cli -D


#2.webpack 基本使用

    npm init    增加package.json文件
    npm init -y


#3.一些语法
    
    ES Module 模块引入方式
    



