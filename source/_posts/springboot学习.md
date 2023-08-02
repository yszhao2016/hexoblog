---
title: springboot学习
date: 2023-08-01 20:20:47
tags:
---

## 创建基于springboot 项目

## 1.maven方式
    pom.xml文件加入
    
      <parent>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-parent</artifactId>
            <version>2.5.6</version>
       </parent>
       
       
       <dependencies>
           <!-- Web 依赖 -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <!-- Web 依赖 -->
       </dependencies>

    更新库就可以
    
 ## 2. 通过官网的spring initializr安装
 
    https://start.spring.io/  
 
 ## 3.idea旗舰版 新建时 可以选
 
 
 
 
 
 ## Spring Boot 应用启动类，
 
 在package创建类加上@SpringBootApplication
 
    package com.zysstudy;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class StartApplication {
        public static void main(String[] args) {
            SpringApplication.run(StartApplication.class,args);
        }
    
    }

 