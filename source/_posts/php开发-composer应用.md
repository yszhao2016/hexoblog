---
title: php开发-composer应用
abbrlink: '89e79247'
date: 2025-08-21 10:02:44
tags:
---


# 一、安装

## 1.1 linux
 ```shell
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
 ```
## 1.2 windows
 ```shell
php -r "readfile('https://getcomposer.org/installer');" | php
move composer.phar C:\ProgramData\ComposerSetup\bin\composer.phar
 ```

#  二、使用
##  2.1 修改源地址

###  2.1.1 修改全局

 ```shell
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
 ```

### 2.1.2 修改当前项目
  ```shell
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
 ```
## 2.2 常用命令

 | 命令 | 作用 |
| --- | --- |
| composer require | 安装依赖包 |
| composer update | 更新依赖包 |


# 三、基于composer包

在composer.json中 autoload 中添加psr-4
示例如下

```json
"autoload": {
    "classmap": [
     "database"
    ],
    "psr-4": {
       "App\\": "app/",
       "Utooo\\": "app/Utooo/",
       "Tests\\": "tests/"
     }
}
```
```php
//app下Utooo文件夹下  Tel文件夹下 Supplier目录下
<?php
namespace Utooo\Tel\Supplier;
class AHMGSupplier extends Supplier {
//业务逻辑
}
```

# 三、创建composer项目



