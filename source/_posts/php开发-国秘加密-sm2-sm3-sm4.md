---
title: php开发-国秘加密(sm2/sm3/sm4)
abbrlink: 6aa2955a
date: 2024-09-03 17:00:31
tags:
categories:
  - PHP开发
---

# 一、SM4加密

     分组对称加密算法，用于数据加密。对应国际算法的 AES。

## 1.1 CBC加密模式

    链式加密：每个明文块与前一个密文块进行异或操作
    需要 IV：需要初始化向量
    错误传播：单个块的错误会影响后续块
    安全性高：隐藏数据模式，推荐使用


## 1.2 ECB加密模式

    避免使用：ECB 模式（除非加密随机数据）

```php
    // sm4 的key 给的是16进制 需要转文本格式(Ascll)
    $secret = hex2bin($this->appSecret);
    $sm4 = new RtSm4($secret);
    return strtoupper($sm4->encrypt($data, 'sm4-ecb'));
```
    


# 二、SM2加密

    基于椭圆曲线的非对称加密算法，用于数字签名和密钥交换。对应国际算法的 RSA、ECDSA

## 2.1 开发问题

    明码也就是没经过 编码过 公钥 私钥

    这边签名的是 私钥 可以是明文的

    pkcs#1秘钥

    pkcs#8秘钥




# 三、SM3加密

    密码杂凑算法（哈希算法），用于生成数字摘要。对应国际算法的 SHA-256。

    