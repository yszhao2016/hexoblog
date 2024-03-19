---
title: docker学习1-Docker基础命令
abbrlink: 2cb0387a
categories:
  - 服务器相关
date: 2023-07-08 21:47:55
tags:
---

# Docker基础命令
    
### 查看镜像
    Docker image ls 

### 查看运行中的容器container

    docker ps      展示 正在运行的

	docker ps -a   所有  启动与未启动的 

    docker ps -q


### 运行容器

docker run 容器名称

docker run -d

    用来在 Docker 中以后台模式运行容器的命令
    
    
docker run -p 8080:80   
    
    -p 宿主机IP:宿主机端口:容器端口
    
    宿主机IP不写表示"0.0.0.0",宿主机PORT不写表示随机端口，
    容器PORT必须指定，可以同时对多个端口进行映射绑定
    指定端口映射，在标准化场景下使用频率高
    
docker run -v

      -v宿主机路径:容器中路径
     
    示例：
        docker run -d -p 8080:80  -v /root/nginx/:/etc/nginx --name nginx-test nginx:1.22

### 查看运行中的容器container

	docker inspect  容器名称    查看容器信息
	
	示例：
	    docker inspect nginx

### 根据dockerfile 文件生成image

	Docker build -t tag/镜像名称   dockerfile所在目录
		示例：
		docker build -t test/hello-word .

### 删除容器

    docker rm 容器ID       

	docker rm $(docker container ls -aq)    删除所有容器
	
	docker rmi image名称   删除镜像


### 进入容器内

    docker exec -it 容器名 /bin/bash
    
    示例：docker exec it php71 /bin/bash



### docker 镜像 导出导入【必须成对使用】

    docker export：导出容器
    docker import：导入容器为镜像

    
    docker save：导出镜像
    
        -o:输出到的文件。

        导出示例：
            docker save -o my_ubuntu_v3.tar runoob/ubuntu:v3
            docker save ubuntu:load>/root/ubuntu.tar

    docker load：导入镜像
        -i：指定导出的文件
        -q：精简输出信息
        
        导入示例：
            docker load -i ubuntu.tar
            docker load < ubuntu.tar
