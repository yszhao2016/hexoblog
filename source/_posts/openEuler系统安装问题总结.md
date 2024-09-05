---
title: openEuler系统安装问题总结
abbrlink: 6c1c44d1
date: 2024-09-05 17:54:37
tags:
---


# 安装 zip 扩展失败报错

报错如下

```js
    yum install php73-php-zip
     
    Last metadata expiration check: 2:08:24 ago on Thu 05 Sep 2024 03:23:50 PM CST.
    Error: 
     Problem: package php73-php-pecl-zip-1.22.3-1.el7.remi.x86_64 requires libzip5(x86-64) >= 1.10.1, but none of the providers can be installed
      - cannot install the best candidate for the job
      - package libzip5-1.10.1-1.el7.remi.x86_64 is filtered out by modular filtering
    (try to add '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)   
```

解决方法：


在/etc/yum.conf文件中追加设置默认platform id，‌如：‌module_platform_id=platform:f30。‌
再次执行dnf makecache，‌错误不再出现。‌‌12
‌Modular dependency problems 解决方案‌

‌问题描述‌：‌存在模块依赖问题，‌如module(platform:el8)无法被提供。‌
‌解决途径‌：‌
检查并确保所有需要的模块和它们的依赖已正确安装。‌
尝试在/etc/yum.repos.d/xx.repo中添加module_hotfixes=1，‌以解决模块依赖问题。‌‌2
