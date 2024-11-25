---
title: linux-防火墙相关
abbrlink: 10974f2f
date: 2024-03-25 18:55:25
tags:
 - linux
categories:
  - 服务器相关  
  - 防火墙
---
# 基础

UFW是Debian系列的默认防火墙

firewall 是红帽系列7及以上的防火墙（如CentOS7.x）

iptables是红帽系列6及以下（如CentOS6.x）的防火墙


firewalld、ufw 与iptables的关系，可以理解为两者只是对iptables其进行了一层封装，
它们在用户交互方面做了非常多的改进，使其对用户更加友好，不需要再记住原来那么多命令了


# ufw相关命令



# firewalld相关命令



# 记奇葩问题

    配置了ufw 
    vim /etc/ufw/before.rules
    
    *nat
    :PREROUTING ACCEPT [0:0]
    :POSTROUTING ACCEPT [0:0]
    -A PREROUTING -p tcp --dport 63301 -j REDIRECT --to-port 22
    #-A PREROUTING -p tcp -m tcp --dport 63306 -j DNAT --to-destination 127.0.0.1:22
    #-A POSTROUTING -j MASQUERADE
    COMMIT

    生效后iptables 即使ufw 删除 nat 记录还是存在
    
    查看iptables nat表
    sudo iptables -t nat -L -n --line-numbers
