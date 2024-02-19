---
title: ansible学习-playbook之nginx配置
abbrlink: '45805688'
categories:
  - 服务器相关
date: 2024-01-17 15:41:48
tags:
---


```
---
- name: install nginx
  hosts: test
  gather_facts: yes      
#  vars:
#       createuser:
#        - tomcat
#        - www
#        - mysql
  vars:
   username: www      
  tasks:
   - name: Print ansible_os_family
     debug:
   #利用循环创建多个用户
   # - name: create user
   #  user: name={{item}} state=present
   #  with_items: "{{createuser}}"     
   - name: Create Nginx User
     user: name={{username}} state=present createhome=no shell=/sbin/nologin
   - name: template config to remote hosts
     template: src=template/nginx.conf.j2 dest=/etc/nginx/nginx.conf
#   - name: add virtualhost config
#      copy: src=my.conf dest=/etc/nginx/conf.d/
#      tags: updateconfig
   #检查nginx是否运行
   - name: check nginx running
     stat: path=/var/run/nginx.pid
     register: nginxrunning
     tags: updateconfig
   #检查nginx配置文件是否有问题          
   - name: check nginx syntax
     command: /usr/sbin/nginx -t
     register: nginxsyntax   
     check_mode: False   
#     changed_when: true
#     ignore_errors: true   
     tags: updateconfig
#删除上一步产生的pid
#   - name: delete pid
#     shell: rm -rf /var/run/nginx.pid
   #debug  nginx状态
   - name: print nginx syntax
     debug:
      var: "nginxrunning, nginxsyntax"
   - name: reload nginx server
     service: name=nginx state=reloaded
     when: nginxsyntax.rc == 0  and nginxrunning.stat.exists == true
     tags: updateconfig     
   - name: start nginx server
     service: name=nginx state=started
     when: 
      - nginxsyntax.rc == 0
      - nginxrunning.stat.exists == false
     tags: updateconfig
     

```