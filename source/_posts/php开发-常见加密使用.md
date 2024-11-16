---
title: php开发-常见加密使用
categories:
  - PHP开发
abbrlink: 78f26761
date: 2024-02-19 17:49:50
tags:
---

# 基础知识

    数字签名 就是明文+私钥密文  这样就能保证数据没有被篡改  
    
    非对称加密 RSA、DSA
        
        公钥、私钥
    
    对称加密 DES、AES
    
        只需要一个秘钥  加密解密 多用它就行了
    
    密码散列算法有 SHA-1、SHA-256 、MD5  
        算出来就不可逆了，主要用来确定来源的
    
    
    
# 签名算法-SHA1withRSA
    
用SHA算法进行签名，用RSA算法进行加密

```php
        $str        = chunk_split($myPrivateKeyStr, 64, "\n");
        $privateKey = "-----BEGIN RSA PRIVATE KEY-----\n$str-----END RSA PRIVATE KEY-----\n";
        openssl_sign($paramStr, $sign, $privateKey);
        $resSign = base64_encode($sign);
```

# RSA加密
```php
    /**
     * RSA分段加密
     *
     * @param $context string 待加密字符串
     * @param $type string  'private' or 'public'
     * @param $key string 公钥 or 私钥
     */
    public static function encrypt($context, $type, $key)
    {
        //http://www.php.net/manual/en/function.openssl-public-encrypt.php#95307
        //Assumes 1024 bit key and encrypts in chunks.
        $maxlength = 117;
        $output = '';
        while ($context) {
            $input = substr($context, 0, $maxlength);
            $context = substr($context, $maxlength);
            if ($type == 'private') {
                $ok = openssl_private_encrypt($input, $encrypted, $key);
            } else {
                $ok = openssl_public_encrypt($input, $encrypted, $key);
            }
            $output .= $encrypted;
        }
        return base64_encode($output);
    }
```

# AES加密
openssl_encrypt($data,$method,$key,$options=0,$iv="",&$tag=NULL,$aad="",$tag_length=16)

1.$data：加密明文

2.$method：加密方法： 可以通过openssl_get_cipher_methods()获取有哪些加密方式

3.$passwd：加密密钥[密码]

4.$options：数据格式选项（可选）【选项有：】：0,OPENSSL_RAW_DATA=1,OPENSSL_ZERO_PADDING=2,OPENSSL_NO_PADDING=3

5.$iv：密初始化向量（可选),需要注意：如果method为DES−ECB，则iv无需填写

6.$tag：使用 AEAD 密码模式（GCM 或 CCM）时传引用的验证标签(可选)

7.$aad：附加的验证数据。（可选）

8.$tag_length：验证 tag 的长度。GCM 模式时，它的范围是 4 到 16(可选)

```php
        openssl_encrypt(json_encode($body), 'AES-128-CBC', $this->key, OPENSSL_RAW_DATA, $this->iv)
        
        bin2hex(openssl_encrypt($str, 'DES-ECB', $this->appSecret, OPENSSL_RAW_DATA));
        
        #一般多需要转换为base64传输
        base64_encode(openssl_encrypt(json_encode($body), 'AES-128-CBC', $this->key, OPENSSL_RAW_DATA, $this->iv));
```
    
    
# DES加密

填充模式：pkcs5、pkcs7、iso10126、ansix923、zero。

加密模式：DES-ECB、DES-CBC、DES-CTR、DES-OFB、DES-CFB。

输出类型：无编码，base64编码，hex编码。

java 下默认是DES-ECB

    bin2hex(openssl_encrypt($str, 'DES-ECB', $this->appSecret, OPENSSL_RAW_DATA));
    


# HMAC 散列消息身份验证码

它不是散列函数，而是采用了将MD5或SHA1散列函数与共享机密秘钥（与公钥/秘钥对不同）一起使用的消息身份验证机制。

目前hmac主要应用在身份验证中，在用户登录传递密码的过程中可以利用，签名来防止密码明文传递。

在php中hash_hmac函数就能将HMAC和一部分哈希加密算法相结合起来实现HMAC-SHA1  HMAC-SHA256 HMAC-MD5等等算法

string hash_hmac(string $algo, string $data, string $key, bool $raw_output = false)

algo：要使用的哈希算法名称，可以是上述提到的md5,sha1等

data：要进行哈希运算的消息，也就是需要加密的明文。

key：使用HMAC生成信息摘要是所使用的密钥。

raw_output：该参数为可选参数，默认为false，如果设为true，则返回原始二进制数据表示的信息摘要，
否则返回16进制小写字符串格式表示的信息摘要（注意是16进制数，而非简单的字母加数字）。


```php
    
    方案一：hash_hmac("sha1", $str, $secret)
    方案二：bin2hex(hash_hmac("sha1", $str, $secret, true));
    
    hash_hmac( 'md5',$data, $this->appSecret)
    
    hash_hmac('sha256', $data,$this->appSecret)
```
  
