---
title: DNS问题
date: 2024-05-25 13:35:34
tags:
---

# ubuntu 系统 

## 传统方法
   /etc/resolv.conf  可能被重置
   
   
   查看是否启用systemd-resolved服务、NetworkManager服务
   
   systemctl status  systemd-resolved   用于提供本地DNS解析服务
   
   systemctl status  NetworkManager
   
   
## 方案一
   
   永久修改DNS方法
   
   /etc/systemd/resolved.conf  

   配置 指定 DNS 服务器，以空白分隔，支持 IPv4 或 IPv6 位置
   DNS=114.114.114.114 8.8.8.8
    
   重启服务生效
   systemctl restart systemd-resolved


## 方案二
    
   /etc/netplan 目录下【00-installer-config.yaml、其他yaml 有可能覆盖】
   
   配置示例：
   
       # This is the network config written by 'subiquity'
       network:
         ethernets:
           ens160:
             dhcp4: false
             addresses:
               - 192.168.100.203/24
             routes:
               - to: default
                 via: 192.168.100.1
             nameservers:
               addresses: [114.114.114.114、8.8.8.8]
         version: 2
    
   配置完执行
       sudo netplan apply
       
##  问题
    
    如果重启 不生效 重置
    
    检查 /etc/cloud/cloud.cfg 文件中的 reslv_conf 注释
    
    
## 验证

    resolvectl status
    
    nslookup            
       
# redhat/centos系统


    /etc/resolv.conf


    /etc/sysconfig/network-scripts/ifcfg-ens
    
    
    验证
    nslookup www.baidu.com