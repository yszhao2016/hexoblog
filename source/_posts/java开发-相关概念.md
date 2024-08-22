---
title: java开发-相关概念
abbrlink: 1f1744f1
date: 2023-02-08 11:39:50
tags:
---
# 一、基本概念
    
1.JavaEE 全称 Java Platform, Enterprise Edition，
    
    它是对 JavaSE(Java Platform, Standard Edition) 的扩展
    加入了面向企业开发（实际上就是网络和 Web 有关开发）的支持，包括 Servlet，WebSocket，EL，EJB 等
    
2.Servlet 和 Servlet Container

    Servlet
    
    Servlet 是一套用于处理 HTTP 请求的 API 标准
    JavaEE 当中只提供了 Servlet 的标准，
    要真正运行 Servlet，需要使用 Servlet Container
    
    Servlet Container
    
    如果 Servlet 是电器，Servlet Container 就是电源插座
    这层抽象让 Servlet 可以跑在任何一个 Container 当中，隔绝了对 Runtime 环境的依赖
    比较常用支持 Servlet Container 的 Server 软件有 Apache Tomcat，Glassfish，JBoss，Jetty 等等。
    
    
    Servlet 是 Java Web 开发的事实标准，不过也不代表所有 Java Web 框架都一定要使用或者兼容 Servlet。
    不使用 Servlet 也可以进行 Java Web 开发，例如 Play Framework，就是完全自立门户的一个框架。
    
3.EJB 和 EJB Container

    EJB 全称 Enterprise JavaBean，和 Servlet 一样，也是 JavaEE 当中的一个组件，面向更加复杂的企业业务开发
    有一个概念需要明确，和 Servlet 类似，运行 EJB 也需要专门的 EJB Container
    Apache Tomcat 不支持 EJB，而 JBoss 提供了对 EJB 的支持。
    
    
4.JVM：java虚拟机，加载.class文件字节码文件并运行.class字节码文件    
    
    
    
# 二、在系统跑Java程序必要软件

Java Development Kit (JDK) 是Java开发工具包,用于开发和编译和调试Java程序

    编译器（javac）：将Java源代码编译成字节码文件（.class文件），以便能够在Java虚拟机（JVM）上执行
    JDK提供了一些调试工具，用于帮助开发人员在开发和测试Java程序时进行调试。
    JDK用于开发和编译和调试Java程序
    
Java Runtime Environment (JRE)：JRE包含了运行Java程序所需的运行时环境。 用于运行Java程序
    
    JRE：java 程序运行时环境，包括JVM虚拟机（java.exe等）和基本的类库（rt.jar等）。
    只包含Java虚拟机（JVM）和Java标准类库，用于执行Java程序
    JRE则用于运行Java程序。
# 三、JavaEE Servlet 应用

     package，然后在 package 上右键，创建一个 Java Class
     
     Servlet API 是包含在 JavaEE 当中的。为了方便，我们直接使用 Tomcat 附带的 servlet-api.jar 包。
     
     
## 3.1 Servlet接口

    import javax.servlet.*;
    public class MyFirstServlet implements Servlet{
    
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

## 3.2 GenericServlet抽象类

GenericServlet使编写Servlet变得更容易。
它提生命周期方法init和destroy的简单实现，要编写一般的Servlet，只需要重写service方法即可。

    import javax.servlet.GenericServlet;
    public class classname extends GenericServlet{
    }     

## 3.2 HttpServlet类  
   
    
HttpServlet是在继承GenericServlet的基础上进一步的扩展。 
提供将要被子类化以创建适用于Web站点的HTTP servlet的抽象类    
要重写doGet和doPost方法


HTTPServlet的子类至少必须重写一个方法，该方法通常是：

doGet()：用于HTTP GET请求

doPost()：用于HTTP POST请求

doPut()：用于HTTP PUT请求

doDelete()：用于HTTP DELETE请求


    import javax.servlet.http.HttpServlet; 
    public class classname extends HttpServlet{
    
    }   
 


    
    

