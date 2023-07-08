---
title: docker学习2
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

## LABEL语法

	LABEL maintainer="xiaoquwl@gmail.com"
	LABEL version="1.0"
	LABEL description="This is description"
	注：Metadata不可少


## RUN 语法

	RUN yum update && yum install -y vim \
	python-dev 
	注：反斜杠换行

	RUN








