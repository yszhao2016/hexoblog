---
title: php开发-微信支付配置
tags:
  - 微信
categories:
  - PHP开发
abbrlink: b1a60784
date: 2025-04-22 11:32:50
---

# 支付Api相关参数申请

![lena](../pic/wx-pay-1.png)

【账号中心】-》 【API安全】


商户API证书  对应   privateKeyPath

商户APIv2秘钥  对应 mchKey

验证微信支付身份  可以不启用 ，若启用 需要修改请求方式  加入这个公钥

平台证书、微信支付公钥  模式多是用来验签的。
平台证书原有的[平台证书有效期为5年]，微信支付公钥模式升级功能
(不要轻易升级，可以导致签名验签失败)

解密回调-》APIv3秘钥     对应 apiV3Key


 ```bash
 pay:
    mchId: 商户号
    mchKey: 0E93E73BCFF80D67D676289957A9B274                        商户秘钥/商户APIv2秘钥
    apiV3Key: 8D55C3D951565A8C6CF1792D37D0C445                      APIv3秘钥 
    privateKeyPath: cert/apiclient_key.pem                          API私钥
    publicKeyPath: cert/pub_key.pem                                 微信支付公钥
    publicKeyId: PUB_KEY_ID_0117104098032025031900389200002398      验证微信支付身份
    merchantSerialNumber: 1D554C3725C800F4CE3F1544426F7100A3FD99DA  API证书的序列号

```