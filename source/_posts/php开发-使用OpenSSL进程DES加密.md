---
title: php开发-使用OpenSSL进程DES加密
abbrlink: 28dcf7b8
date: 2024-11-17 17:10:10
tags:
---

# 一、遇到问题DES 加密返回空

## 1.1加密代码

    bin2hex(openssl_encrypt($str, 'DES-ECB', $this->appSecret, OPENSSL_RAW_DATA));
    
## 1.2 解决方案
    
1.检查 openssl 库是否安装

 php -m|grep openssl
 
2.过 <font color="red">openssl_error_string() </font>调试代码 处理


## 1.3报错 【error:0308010C:digital envelope routines::unsupported】

<font color="red">原因是: 服务器上的openssl是1.1版本, 本地是3.0版本</font>

找到 openssl.cnf文件

可以通过phpinfo找到openssl.cnf 或者 php -i|grep  openssl.cnf

找到[provider_sect]并将其更改为以下内容
```apacheconfig
[provider_sect]
default = default_sect
legacy = legacy_sect
```

找到[default_sect]并将其更改为以下内容
```apacheconfig
[default_sect]
activate = 1
[legacy_sect]
activate = 1
```

# 二、DES加密、解密php中使用


## 2.1

加密模式：DES-ECB
填充模式：PKCS#7 


```php
// pkcs#7
bin2hex(openssl_encrypt("加密字符串", 'DES-ECB', "key字符串", OPENSSL_RAW_DATA));
```



```php
$key = str_pad($key,24,'0');
bin2hex(openssl_encrypt("加密字符串", 'DES-ECB', "key字符串", OPENSSL_RAW_DATA));

$encrypted = pack('H*', $info);
$key = str_pad($key,24,'0');

```
## 2.2      