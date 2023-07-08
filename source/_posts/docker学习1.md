---
title: docker学习1
date: 2023-07-08 21:47:55
tags:
---

# Docker基础命令
    
### 查看镜像
    Docker image ls 

### 查看运行中的容器container

    docker ps       

	docker ps -a   所有  启动与未启动的 


### 运行容器

    docker run 容器名称

### 查看运行中的容器container

	docker inspect  容器名称    查看容器信息

### 根据dockerfile 文件生成image

	Docker build -t tag/镜像名称   dockerfile所在目录
		示例：
		docker build -t test/hello-word .

### 删除容器

    docker rm 容器ID       

	docker rm $(docker container ls -aq)    删除所有容器
	
	docker rmi image名称   删除镜像
