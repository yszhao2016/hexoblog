---
title: linux-firewalld使用总结
abbrlink: 34fa5185
date: 2024-01-09 16:27:09
tags:
---


# 查看放开端口

    firewall-cmd --list-ports
    
    
# 添加放开的端口 

    firewall-cmd --permanent --add-port=8001/tcp   