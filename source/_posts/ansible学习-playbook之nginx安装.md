---
title: ansible学习-playbook之nginx安装
abbrlink: '78909e13'
categories:
  - 服务器相关
date: 2024-01-17 15:41:33
tags:
---

```
---
 - name: install nginx
   hosts: test
   tasks:
    - name: Print ansible_os_family
      debug:
       var: ansible_os_family    
    #关闭selinux     
    - name: disable selinux
      shell: "setenforce 0"
      ignore_errors: true
    - name: Update apt package cache
      apt:
        update_cache: yes
      ignore_errors: true
      when: ansible_os_family in ["Debian","ubuntu"]     
    - name: ubuntu install nginx
      apt:
        name: nginx
        state: present   
      when: ansible_os_family in ["Debian","ubuntu"]
    - name: Add repo 
      yum_repository:
       name: nginx
       description: nginx repo
       baseurl: http://nginx.org/packages/centos/7/$basearch/
       gpgcheck: no
       enabled: 1
      when: ansible_os_family in  ["RedHat","openEuler"]
    - name: redhat install nginx
      yum:
       name: nginx
       state: latest
      when: ansible_os_family in ["RedHat","openEuler"]
    - name: start nginx server
      service: name=nginx state=started
```
