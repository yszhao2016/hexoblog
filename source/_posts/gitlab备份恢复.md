---
title: gitlab备份恢复
abbrlink: 6551734d
date: 2024-06-28 22:14:05
tags:
---

# gitlab 备份命令

## 通过源码安装-备份命令

bundle exec rake gitlab:backup:create RAILS_ENV=production

    注   bundle exec 是一个用于执行 Rails 或其他 Ruby 项目的命令
    
         bundle exec rake  是一个用于执行 Ruby on Rails 项目中的 Rake 任务的命令。
         Rake 是 Ruby 中一个构建工具，用于自动化任务、数据库迁移、部署等操作
         
         bundle exec rails s
         这会在 Rails 项目中启动一个本地服务器
         
         
         
## 有gitlab-rake 命令工具

gitlab-rake gitlab:backup:create


## 安装在docker中 可以用
    
后台运行   
sudo docker exec -d a51fe5f513bc bundle exec rake gitlab:backup:create RAILS_ENV=production


sudo docker exec a51fe5f513bc  /bin/bash -c "bundle exec rake gitlab:backup:create RAILS_ENV=production"



# 备份所在目录

通常在/var/opt/gitlab/backups目录下

在gitlab 目录下 config/gitlab.yml 有个

backup:
    path: "/home/git/data/backups" 
    
    
# 恢复备份


    