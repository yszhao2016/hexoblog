---
title: php开发-docker环境搭建
abbrlink: f4cbc520
date: 2025-04-17 08:49:45
tags:
---
# 一、php环境搭建

```dockerfile
FROM php:7.4-fpm
RUN apt-get update
RUN apt-get  install -y  vim  libfreetype6-dev libjpeg64-turbo-dev libpng-dev
RUN docker-php-ext-install -j$(nproc) iconv && docker-php-ext-configure gd --with-freetype-dir=/user/include/ --with-jpeg-dir=/usr/include/ &&  docker-php-ext-install -j$(nproc) gd
RUN  docker-php-ext-configure pdo_mysql &&docker-php-ext-install pdo-mysql
RUN pecl install swoole && pecl install redis-4.3.0
RUN docker-php-ext-enable redis swoole
RUN groupadd www && \
useradd -g www -s /sbin/nologin www && \
usermod -u 8002 www && groupmod -g 8002 www

```

```bash
docker run  \
-v /docker/php/etc:/usr/local/etc \
-v /docker/nginx/www:/var/www/html  \
--privileged=true \
--restart=always \
--name php71 \
-d php71:1.3
```




# 二、nginx环境搭建

```dockerfile
FROM  nginx:1.24
RUN groupadd www && \
useradd -g www -s /sbin/nologin www && \
usermod -u 8002 www && groupmod -g 8002 www
```


```bash
docker run  -p 8088:8088 -v /docker/nginx/www:/usr/share/nginx/html  \
-v /docker/nginx/nginx.conf:/etc/nginx/nginx.conf  \
-v /docker/nginx/logs/:/var/log/nginx  \
-v /docker/nginx/conf.d:/etc/nginx/conf.d  \
--link php74 \
--privileged=true \
--restart=always \
--name nginx \
-d nginx:1.24
```



# 三、MySQL环境搭建