---
title: java学习3-Servlet及HttpSeverlet
abbrlink: e9f2e524
date: 2024-04-10 15:17:32
tags:
---

# 一、Servlet

## 1.1基础知识

Servlet是什么呢？Servlet就是一个服务端的小组件，这个小组件可以接受客户端发过来的信息，然后对其进行处理。

Servlet是一个抽象类，它的各种方法要由它的继承类和接口来实现。

## 1.2继承关系
 javax.servlet.Servlet接口

​ javax.servlet.GenericsServlet 抽象类

​ javax.servletHttpServlet

## 1.3Servlet有下面三种方法：
   init （初始化方法）启动servlet 容器执行一次
   service（服务方法）
   destory（销毁方法）关闭servlet 容器 执行
   
## 1.3示例代码   
```java

package com.study;
import javax.servlet.*;
import java.io.IOException;
import java.io.PrintWriter;
public class MyFirstServlet implements Servlet {
    public void init(ServletConfig config) throws ServletException {
        System.out.println("Init");
    }
    public void service(ServletRequest request, ServletResponse response)
            throws ServletException, IOException {
        System.out.println("From service");
        PrintWriter out = response.getWriter();
        out.println("Hello, Java Web.");
    }
    public void destroy() {
        System.out.println("Destroy");
    }
    public String getServletInfo() {
        return null;
    }
    public ServletConfig getServletConfig() {
        return null;
    }
}

```

# 二、HttpServlet

## 1.1基础知识

HttpServlet 实现了 Servlet 
HttpServlet可以理解为专门处理Http文件的服务器组件Servlet
<font color="red">如果客户端有信息发过来的话，HttpServlet会被调用的只有Servlet方法，
然后Servlet方法就会去判断对面发过来的请求是什么，调用doGet 还是doPost等等</font>
## 2.1HttpServlet有的方法：
init()
service()  每次收到路径匹配的请求都要调用一次。
destory()
doGet(),
doPost(),
doPut/doDelete/doOption
## 2.2 HttpServletRequest
## 2.3HttpServletResponse
## 2.4 示例代码

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
 
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html;charset=utf8");
        resp.getWriter().write("这是一个doGet方法");
    }
 
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("这是一个doPost方法");
    }
 
    @Override
    protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("这是一个doPut");
    }
 
    @Override
    protected void doDelete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("这是一个doDelete方法");
    }
}
```