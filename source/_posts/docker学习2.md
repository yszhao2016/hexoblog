---
title: docker学习2-DockerFile
abbrlink: ad46b1ee
categories:
  - 服务器相关
date: 2023-07-08 21:54:55
tags:
---

# Dockerfile 学习


## 相关语法

	FROM  scratch
	ADD hello /
	CMD ["/hello"]


## FROM语法

	FROM scratch   //制作  base image

	FROM centos    //使用  base image

	FROM ubuntu:14.4

## LABEL语法（类似注释作用）

	LABEL maintainer="xiaoquwl@gmail.com"
	LABEL version="1.0"
	LABEL description="This is description"
	注：Metadata(元数据)不可少

    示例：
    LABEL org.opencontainers.image.authors="yeasy"
    LABEL org.opencontainers.image.documentation="https://yeasy.gitbooks.io"


## RUN 语法

	RUN yum update && yum install -y vim \
	python-dev 
	注：反斜杠换行

	RUN


## WORKDIR 语法
    设置工作目录，目录不存在，会自动创建

    示列：
    WORKDIR /test
    WORKDIR abc
    这样就工作在/test/abc目录下
    注：
        1、RUN cd 能实现类似效果 但不推荐
        2、目录尽量示绝对目录

## ADD&&COPY 语法

    功能类似添加文件到容器
    ADD 有解压缩功能

    示列：
     ADD test.tar.gz /app/web
    
## ENV 语法
    
    定义变量
    示列：
        ENV  MYSQL_VERSION 5.6
        RUM yum install mysql_server="${MYSQL_VERSION}"


## VOLUME&&EXPOSE
    存储和网络



