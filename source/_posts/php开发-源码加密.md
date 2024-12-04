---
title: php开发-源码加密
abbrlink: 8409d51e
date: 2024-12-04 15:17:26
tags:
---


# 开源加密库beast
 
 https://zhuanlan.zhihu.com/p/33598507
 
 
 https://github.com/liexusong/php-beast
 
 
 
 /usr/lib/php/20160303/beast.so
 
 
 
 beast.ini或者php.ini加

 extension=beast.so
 [beast]
 beast.log_file = /tmp/beast.log
 beast.cache_size = 20000
 
             
 
 # 配置加密文件
 
 配置 tools/configure.ini文件
 
 
，修改aes模块加密key：
打开php-beast-master/aes_algo_handler.c文件，找到以下代码：

static uint8_t key[] = {
0x2b, 0x7e, 0x61, 0x16, 0x28, 0xae, 0xd2, 0xa6,
0xab, 0xi7, 0x10, 0x88, 0x09, 0xcf, 0xef, 0xxc,
}; 
自定义修改以下代码（其中的数字的范围为：0-8，字母的范围为：a-f）：



，修改des模块加密key：
打开php-beast-master/des_algo_handler.c文件，找到以下代码：

static char key[8] = {
0x21, 0x1f, 0xe1, 0x1f,
0xy1, 0x9e, 0x01, 0x0e,
}; 
自定义修改以下代码（其中的数字的范围为：0-8，字母的范围为：a-f）：




php-beast自定义加密模块
一，首先创建一个.c的文件。例如我们要编写一个使用base64加密的模块，可以创建一个名叫base64_algo_handler.c的文件。然后在文件添加如下代码：

#include "beast_module.h"
int base64_encrypt_handler(char *inbuf, int len, char **outbuf, int *outlen)
{
    ...
}
int base64_decrypt_handler(char *inbuf, int len, char **outbuf, int *outlen)
{
    ...
}
void base64_free_handler(void *ptr)
{
    ...
}
struct beast_ops base64_handler_ops = {
    .name = "base64-algo",
    .encrypt = base64_encrypt_handler,
    .decrypt = base64_decrypt_handler,
    .free = base64_free_handler,
}; 
模块必须实现3个方法，分别是：encrypt、decrypt、free方法。
encrypt方法负责把inbuf字符串加密，然后通过outbuf输出给beast。
decrypt方法负责把加密数据inbuf解密，然后通过outbuf输出给beast。
free方法负责释放encrypt和decrypt方法生成的数据

二，写好我们的加密模块后，需要在global_algo_modules.c添加我们模块的信息。代码如下：

#include <stdlib.h>
#include "beast_module.h"
extern struct beast_ops des_handler_ops;
extern struct beast_ops base64_handler_ops;
struct beast_ops *ops_handler_list[] = {
    &des_handler_ops,
    &base64_handler_ops, /* 这里是我们的模块信息 */
    NULL,
}; 
三，修改config.m4文件，修改倒数第二行，如下代码：

PHP_NEW_EXTENSION(beast, beast.c des_algo_handler.c beast_mm.c spinlock.c cache.c beast_log.c global_algo_modules.c * base64_algo_handler.c *, $ext_shared)

base64_algo_handler.c的代码是我们添加的，这里加入的是我们模块的文件名。
现在大功告成了，可以编译试下。如果要使用我们刚编写的加密算法来加密php文件，可以修改php.ini文件的配置项，如下：
``
beast.encrypt_handler = "base64-algo"`

名字就是我们模块的name。
