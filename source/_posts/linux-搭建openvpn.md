---
title: 搭建openvpn
abbrlink: 224c29d4
date: 2023-08-28 13:42:06
tags:
 - linux
categories:
  - 服务器相关  
---
# 一、安装
## 1.1、安装epel库和openvpn软件

    yum -y install openvpn easy-rsa firewalld

    yum install epel-release -y
 
 
## 1.2、开启路由转发

     echo  "net.ipv4.ip_forward = 1" >>/etc/sysctl.conf
     sysctl -p
 
## 1.3、创建OpenVPN相关的密钥

     cp -rf /usr/share/easy-rsa/3.0/* /etc/openvpn/easy-rsa/
     cp -p /usr/share/doc/easy-rsa-3.0.6/vars.example /etc/openvpn/easy-rsa/vars
     cd /etc/openvpn/easy-rsa/
 
 
 ### 1.3.1、创建ca证书
 
    /usr/share/easy-rsa/3/easyrsa init-pki
    /usr/share/easy-rsa/3/easyrsa build-ca nopass


### 1.3..2、创建client证书和签名

    /usr/share/easy-rsa/3/easyrsa gen-req client1 nopass           #Enter
    /usr/share/easy-rsa/3/easyrsa sign-req client client1          #需要输入yes
    创建dh、tls、crl（证书撤销秘钥）
    /usr/share/easy-rsa/3/easyrsa gen-dh
    openvpn --genkey --secret /etc/openvpn/easy-rsa/ta.key
    /usr/share/easy-rsa/3/easyrsa  gen-crl


### 1.3.3、将秘钥拷贝至相应目录

    cp -p pki/ca.crt /etc/openvpn/server/
    cp -p pki/issued/server1.crt /etc/openvpn/server/
    cp -p pki/private/server1.key /etc/openvpn/server/
    cp -p ta.key /etc/openvpn/server/
    cp -p pki/ca.crt /etc/openvpn/client/
    cp -p pki/issued/client1.crt /etc/openvpn/client/
    cp -p pki/private/client1.key /etc/openvpn/client/
    cp -p ta.key /etc/openvpn/client/
    cp pki/dh.pem /etc/openvpn/server/
    cp pki/crl.pem /etc/openvpn/server/
    cp /usr/share/doc/openvpn-2.4.11/sample/sample-config-files/server.conf /etc/openvpn/
    
    
# 二、配置 openvpn

## 2.1、编辑配置文件 /etc/openvpn/server.conf   

    cp /usr/share/doc/openvpn-2.4.11/sample/sample-config-files/server.conf /etc/openvpn/      
    #拷贝实例修改或直接拷贝以下内容
    vim /etc/openvpn/server.conf

    local 0.0.0.0    #监听地址
    port 1194        #监听端口
    proto tcp        #监听协议
    dev tun          #采用路由隧道模式
    ca /etc/openvpn/server/ca.crt              #ca证书路径
    cert /etc/openvpn/server/server1.crt       #服务器证书
    key /etc/openvpn/server/server1.key        #服务器秘钥
    dh /etc/openvpn/server/dh.pem              #密钥交换协议文件
    #####注意在设备中加一条到10.100.100.0虚拟网段的路由！！！！
    server 10.100.100.0 255.255.255.0          
    ####给客户端分配地址池，注意：不能和VPN服务器内网网段有相同
    #####注意在设备中加一条到10.100.100.0虚拟网段的路由！！！！
    ifconfig-pool-persist ipp.txt
    #push "redirect-gateway def1 bypass-dhcp"      #推送默认路由（所有流量走vpn）
    push "route 192.168.0.0 255.255.255.0"           
    #推送客户端常用路由（根据自己内网网段来写或修改客户端配置添加路由）
    push "dhcp-option DNS 192.168.5.251"           #dhcp分配dns
    client-to-client       #客户端之间互相通信
    keepalive 10 120       #存活时间，10秒ping一次,120 如未收到响应则视为断线
    comp-lzo               #传输数据压缩
    max-clients 100        #最多允许 100 客户端连接
    user openvpn           #用户
    group openvpn          #用户组
    cipher AES-256-CBC     #加密方式--客户端必须一致
    persist-key
    persist-tun
    status /var/log/openvpn-status.log
    log         /var/log/openvpn.log
    verb 3
    #以下参数为用户认证使用
    script-security 3
    auth-user-pass-verify /etc/openvpn/checkpsw.sh via-env    #指定用户认证脚本
    username-as-common-name
    verify-client-cert none
 
 ## 2.2虚拟网段的路由
    
    iptables -t nat -A POSTROUTING -s 10.100.100.0/24 -j MASQUERADE
   
   
    firewall-cmd --add-masquerade --permanent
    firewall-cmd --query-masquerade --permanent
    
    firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="10.100.100.0/24" port protocol="tcp" accept'
    firewall-cmd --zone=public --add-masquerade
    
# 三、用户认证
    
 ## 3.1编写用户认证脚本文件 (脚本是由openvpn官网提供)
 
    vim /etc/openvpn/checkpsw.sh
    #!/bin/sh
    ###########################################################
    # checkpsw.sh (C) 2004 Mathias Sundman 
    # This script will authenticate OpenVPN users against
    # a plain text file. The passfile should simply contain
    # one row per user with the username first followed by
    # one or more space(s) or tab(s) and then the password.
    
    PASSFILE="/etc/openvpn/psw-file"
    LOG_FILE="/etc/openvpn/openvpn-password.log"
    TIME_STAMP=`date "+%Y-%m-%d %T"`
    ###########################################################
    
    if [ ! -r "${PASSFILE}" ]; then
      echo "${TIME_STAMP}: Could not open password file \"${PASSFILE}\" for reading." >>${LOG_FILE}
      exit 1
    fi
    
    CORRECT_PASSWORD=`awk '!/^;/&&!/^#/&&$1=="'${username}'"{print $2;exit}' ${PASSFILE}`
    
    if [ "${CORRECT_PASSWORD}" = "" ]; then 
      echo "${TIME_STAMP}: User does not exist: username=\"${username}\", password=\"${password}\"." >> ${LOG_FILE}
      exit 1
    fi
    
    if [ "${password}" = "${CORRECT_PASSWORD}" ]; then 
      echo "${TIME_STAMP}: Successful authentication: username=\"${username}\"." >> ${LOG_FILE}
      exit 0
    fi
    
    echo "${TIME_STAMP}: Incorrect password: username=\"${username}\",     password=\"${password}\"." >> ${LOG_FILE}
    exit 1

 ## 3.2 加权限及添加用户账号
 
    给脚本添加执行权限
    chmod 755 /etc/openvpn/checkpsw.sh
    
    
    编辑密码文件
    vim /etc/openvpn/psw-file
    tom  123456
    Jerry 654321
    #前面为用户名，后面为密码。 中间使用空格分开
  
  
# 四、客户端配置
    client.ovpn
    
    ##---------以下为将ca.crt证书写入到客户端文件的方式（直接给一个client.ovpn文件即可）------------##
    client
    dev tun
    proto tcp   #根据服务端的来
    remote utnanjing.tpddns.cn 11194
    resolv-retry infinite
    nobind
    persist-key
    persist-tun
    <ca>
    -----BEGIN CERTIFICATE-----
    MIIDNTCCAh2gAwIBAgIJAJgy1QReG9VmMA0GCSqGSIb3DQEBCwUAMBYxFDASBgNV
    BAMMC0Vhc3ktUlNBIENBMB4XDTIzMDgyNDA5NTgzN1oXDTMzMDgyMTA5NTgzN1ow
    FjEUMBIGA1UEAwwLRWFzeS1SU0EgQ0EwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAw
    ggEKAoIBAQDLutXX/OaN1muPGKQV2uiEaZbQy9F0PptJxM3ZTHdyn+HuQ/3xqYHz
    z6hgIr2IjWtD5pqZXGMwIxbpNFKfPfJtwU5xpuUA6hxWV3yYtaXGsaLvFA0StL/N
    P5/3VgL1KpdcivjMejgVCwX5CkN97hsS6uk9Z/PUB9OEAWygeRHS8CYQ37PhXmDC
    h/1VeXWZDHJzxCD1fqoo2dB8MGFJjvg3FVQ0o8Gf0XE9cvj5dtk73f6AyiuYTeLL
    nD8vY8jbCVGeLcnoDnduK6y6ZLFOUHWfEPH+a1jDFLpXIby0oo3yBWcldzaKJMzf
    UP7HoczkDaUo5ykecDjxGkD8kpTmwyQPAgMBAAGjgYUwgYIwHQYDVR0OBBYEFPCY
    FNHxZ/eBKLRh009Bm08j8zurMEYGA1UdIwQ/MD2AFPCYFNHxZ/eBKLRh009Bm08j
    8zuroRqkGDAWMRQwEgYDVQQDDAtFYXN5LVJTQSBDQYIJAJgy1QReG9VmMAwGA1Ud
    EwQFMAMBAf8wCwYDVR0PBAQDAgEGMA0GCSqGSIb3DQEBCwUAA4IBAQCYFaym5wCr
    lti+7p9MqLm4UF7pOmZQ2NamZjP1cQuMh2SgdJphddZEhEUO5NOQms8gMJsZ3sd7
    DIul+XkDcbhjKNNxD4oeXiITR8BgLsCkqtLStwWtOSLGxgHHWD3S+IBSM21A4Rxy
    hE4VUr81i9XUM2lI7ml5RgCVTvbWosCnVFuXypaYs4/c1yJJ1I578M6oVWjahJOx
    ENoeYYSI/V+2ySTtRQlW8HQEc4GhSeybhmXgrfLKhmHTVl/ZqZg5zp3kwBcnsrUQ
    junuLBLMMYSLPXhJQFU/10EMxv/9AAdBGiO3RL0G2qYc0JF11qVpmD9nwP9f/YR3
    Yvs7E2kwDViH
    -----END CERTIFICATE-----
    </ca>
    cipher AES-256-CBC
    comp-lzo
    verb 3
    auth-user-pass              #使用用户名密码登录openvpn服务器
    auth-nocache


    证书方式
    
    client
    dev tun
    proto tcp
    remote 公网ipxxx.xxx.xxx.xxx 1194
    resolv-retry infinite
    nobind
    persist-key
    persist-tun
    ca ca.crt
    cert client1.crt  #拷贝自服务端
    key client1.key   #拷贝自服务端 
    remote-cert-tls server
    cipher AES-256-CBC
    comp-lzo
    verb 3
# 五、相关命令

 systemctl start openvpn@server
 systemctl stop openvpn@server
 systemctl restart openvpn@server
 systemctl status openvpn@server

# 六、问题总结
## 一、客户端总报证书错误

检查证书有效期
openssl x509 -in /etc/openvpn/server.crt -noout -dates

openssl x509 -in /etc/openvpn/ca.crt -noout -dates


### 1. 生成CA私钥
openssl genrsa -out ca.key 2048

### 2. 生成CA根证书（自签名，有效期10年）
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt \
-subj "/C=CN/ST=Guangdong/L=Shenzhen/O=MyOrg/CN=MyOpenVPN-CA"

如果ca.key ca.crt 已经存在就只需要接下来步骤

### 3. 生成服务器私钥
openssl genrsa -out server.key 2048

### 4。 创建证书签名请求 (CSR)
openssl req -new -key server.key -out server.csr

### 5. 使用现有 CA 签名（假设您有 ca.key 和 ca.crt）
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 3650

### 6.替换 /etc/openvpn/server.key   /etc/openvpn/server.crt
