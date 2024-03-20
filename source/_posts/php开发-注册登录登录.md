---
title: php开发-注册登录
abbrlink: a634ca62
date: 2024-03-18 15:43:30
tags:
---

# 一、验证码
    
## 1.1 生成
### 随机字符串 方案
```php
    $codeset= "2345678abcdefhijkmnpqrstuvwxyzABCDEFGHJKLMNPQRTUVWXY
     for ($i = 0; $i < $length; $i++) {
            $code[$i] = $codeSet[mt_rand(0, strlen($codeset) - 1)];
     }
     return $code;
 ```
### 加减法 方案

### 拖动拼图方案 
   
## 1.2 存储验证码  

   cookie session 
   
## 1.3 验证验证码   
  
  session
    
    
# 二、注册方式
    
## 用户名+密码+邮箱注册

### 注册处理

    存储数据库 过期没激活 删除 
     
    url 中加个唯一的key  发送邮件
    
### 激活模块

    根据  url 中唯一的key 查询处理

## 用户名+密码+手机验证码
    
    生成验证码 存储  然后发送短信

## 第三方注册


# 登录方式

## 用户名+密码

基于cookie+session 

## 手机号+验证码+手机验证码

PC端 前后端不分离 手机号码 + 验证码  基于cookie+session 

移动端 token 方式 用户名密码 验证成功 就生成$token 
然后$token+$user_id 绑定
生成token 示例：
```php

  /**
     * 获取全球唯一标识
     * @return string
     */
    public static function uuid()
    {
        return sprintf(
            '%04x%04x-%04x-%04x-%04x-%04x%04x%04x',
            mt_rand(0, 0xffff),
            mt_rand(0, 0xffff),
            mt_rand(0, 0xffff),
            mt_rand(0, 0x0fff) | 0x4000,
            mt_rand(0, 0x3fff) | 0x8000,
            mt_rand(0, 0xffff),
            mt_rand(0, 0xffff),
            mt_rand(0, 0xffff)
        );
    }
```

## 本机号码一键登录

## 第三方授权登录

## 手机号+验证码+绑定第三方授权


## 记住功能

设置session的保存时长

# 单点登录

## 直接基于Cookie与Session实现单点登录

## 基于CAS方案实现单点登录

## 基于OAuth方案实现单点登录

# 多终端设备的用户互踢