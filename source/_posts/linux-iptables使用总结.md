---
title: linux-iptables使用总结
abbrlink: 24ccf49a
date: 2023-07-26 15:31:32
tags:
---
一、iptables
    iptables防火墙没有启用,iptables命令仍然会显示默认的规则.


二、常用命令

常用参数

    -n 参数表示将 IP 地址和端口号显示为数字形式，而不是解析为主机名和服务名。
    
    -L 选项用于列出防火墙规则。
    
    -t nat 参数指定了要操作的表为 NAT 表，NAT 表用于处理网络地址转换相关的规则。

iptables -n -L           显示当前防火墙规则列表。
iptables --list

iptables -t nat -n -L    看 iptables 中 NAT（Network Address Translation）表的规则。

    
结果怎么看

    Chain INPUT (policy ACCEPT)
    target     prot opt source               destination         
    ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0            state NEW udp dpt:2333
    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:2333
    
    Chain FORWARD (policy DROP)
    target     prot opt source               destination         
    DOCKER-USER  all  --  0.0.0.0/0            0.0.0.0/0           
    DOCKER-ISOLATION-STAGE-1  all  --  0.0.0.0/0            0.0.0.0/0           
    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    DOCKER     all  --  0.0.0.0/0            0.0.0.0/0           
    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
    
    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination         
    
    Chain DOCKER (1 references)
    target     prot opt source               destination         
    ACCEPT     udp  --  0.0.0.0/0            172.17.0.2           udp dpt:60050
    ACCEPT     udp  --  0.0.0.0/0            172.17.0.2           udp dpt:60049
    ACCEPT     udp  --  0.0.0.0/0            172.17.0.2           udp dpt:60048
    ACCEPT     udp  --  0.0.0.0/0            172.17.0.2           udp dpt:60047
    
    Chain DOCKER-ISOLATION-STAGE-1 (1 references)
    target     prot opt source               destination         
    DOCKER-ISOLATION-STAGE-2  all  --  0.0.0.0/0            0.0.0.0/0           
    RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
    
    Chain DOCKER-ISOLATION-STAGE-2 (1 references)
    target     prot opt source               destination         
    DROP       all  --  0.0.0.0/0            0.0.0.0/0           
    RETURN     all  --  0.0.0.0/0            0.0.0.0/0           
    
    Chain DOCKER-USER (1 references)
    target     prot opt source               destination         
    RETURN     all  --  0.0.0.0/0            0.0.0.0/0 
     
    "LIBVIRT_FWI"是一个iptables防火墙的链，并且该链已经被引用了一次
    
    允许所有协议（all）的流量从任意源IP地址（0.0.0.0/0）进入到目标IP地址为172.17.0.2  60047端口
    
    
    REJECT 拒绝
    
    ACCEPT 允许
    
    DROP   丢弃
    
禁止主机去访问某个ip
    
    iptables -A OUTPUT -d 185.196.8.123 -j DROP  #试了下不知为啥不行
    
    iptables -I OUTPUT -d aheatcorner.world -j REJECT
    
    -A: 在规则链的末尾加入新规则
    
    -I: 在规则链的头部加入新规则
    
删除某个链中某规则    
    
    iptables -D 链名 序号
    
    示例：
    iptables -D OUTPUT  1