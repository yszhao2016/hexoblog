---
title: ansible学习
date: 2023-12-01 19:48:14
tags:
---

## 一、安装

 yum install -y epel-release
 
 yum install -y ansible
 
 ansible配置文件：
 
    /etc/ansible/ansible.cfg
    /etc/ansible/hosts
    
 生成秘钥：
 
    ssh-keygen -t rsa  #在/root/.ssh/目录下生成秘钥
    #被控端：(复制主机端公钥到被控端)
    ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.7.99 
    ssh-copy-id  被控端ip
   
## 二、管理服务器：Inventory文件 
 
用于管理主机与主机组的文件 也叫主机清单
默认文件  /etc/ansible/hosts 最好不要用这  不然一不小心  没指定hosts 就执行这个了

文件示例
```
[gs]
gs157 ansible_ssh_host=1.11.1.11 ansible_ssh_port=65022
[remote]
kh251 ansible_ssh_host=2.22.222.2
```
    
    
## 三、基础命令

###  常用命令

查看具体主机配置

    grep -v ^# /etc/ansible/hosts |grep -v ^$
    
    ansible all --list-host     

### 基础参数
    
-m 指定模块【command copy shell】
<font color="red">   忽略时，就默认是 -m command </font>
   
    示例：  ansible -i ./hosts -a ifconfig  test
   
-a 模块中使用的参数

    示例：  ansible -i ./hosts -m shell -a "free -mh"  test
    
-i 指定hosts文件

    示例  ansible -i ./hosts -m ping test

###  检查能否在受管主机上运行python模块
    ansible host-pattern -m module [-a 'module arguments'] [-i inventory]
    示例
        ansible gsweb -m ping
        
        ansible -i /etc/ansible/hosts gsweb -m ping

       
### command模块 
    执行shell命令
    -m 不使用 默认使用command模块
    
    ansible -m command -a "frem -mh" test   
    
    ansible -m command -a "frem -mh" test     
    
    ansible  -a "frem -mh" test 
### shell模块    

 与command模块有些相似,但是command 有些命令无法执行
    
  命令行示例
    
     ansible -m shell -a "cat /etc/*release" test
    
### copy模块

   参数
    
    src：    待copy的文件或目录
    
    dest：   目标copy到远程主机的具体目录（必要参数）
    
    owner：  目标copy到远程主机后的属主（远程主机上必须有对应的用户，否则报错）
    
    group：  目标copy到远程主机后的属组（远程主机上必须有对应的组，否则报错）
    
    mode：   目标copy到远程主机后的权限

    content： 若不用src指定拷贝的文件，必须用content直接指定文件内容，2选一。 
    
    force：   当远程主机的目标路径中已经存在同名文件，并且与ansible主机中的文件内容不同时，
              是否强制覆盖，可选值有yes和no。默认值为yes，表示覆盖，
              如果设置为no，则不会执行覆盖拷贝操作，远程主机中的文件保持不变。
              
    backup：  当远程主机的目标路径中已经存在同名文件，并且与ansible主机中的文件内容不同时，
              是否对远程主机的文件进行备份，
              可选值有yes和no，当设置为yes时，会先备份远程主机中的文件，
              然后再将ansible主机中的文件拷贝到远程主机。
    

   命令行 示例
   
       ansible khweb  -m copy -a "src=/home/soft/node_exporter-1.6.1.linux-amd64.tar.gz  dest=/home/soft/  backup=yes mode=644"

   playbook 示例
   
       - name: Copy a file from local to remote
         copy:
          src: /path/to/local/file.txt
          dest: /path/to/remote/file.txt
          
          
       - name: "copy files"
             copy:
             src: "{{ item.src }}"
             dest: "{{ item.dest }}"
             owner: root
             group: root
             mode: 755
             with_items:
                - {src: "/root/aaa.txt", dest: "/tmp" }
                - {src: "/root/bbb.txt", dest: "/tmp" }
 
   
### setup模块

### file模块

### debug模块

### yum模块

### user模块

### register模块
    
 register 捕获 task 的输出，并将它保存到一个变量中，方便在以后的任务中调用
     
 change： 表示执行命令的状态，如果命令执行了，则为 true；
 
 cmd： 表示的则是你当前执行的命令；
 
 delta： 表示命令执行所花费的时间
 
 start： 表示命令开始执行的时间
 
 end： 表示命令结束的时间
 
 failed： 表示命令执行的结果，如果为 false 则表示命令执行成功，true 则表示命令执行失败
 
 rc： 表示命令执行的返回码（return code），0 表示执行成功；
 
 stderr：命令输出的标准错误信息
 
 stderr_lines： 按换行符分割输出的内容，在多行输出时，显示的效果比 stderr 更加直观
 
 stdout： 命令的标准输出信息
 
 stdout_lines： 按换行符分割输出的内容，在多行输出时，结果更加直观    
     
    示例：
    - name: View the logged in user name
      shell: whoami
      register: user
    - debug:
        var: user  
         
    - name: check nginx running
       stat: path=/var/run/nginx.pid
       register: nginxrunning   
    - name: check nginx syntax
      shell: /usr/sbin/nginx -t
      register: nginxsyntax
    # check_mode  true  -C测试 会跳过
      check_mode: false
      debug:
        var: "nginxsyntax,nginxrunning"
    - name: reload nginx server
      service: name=nginx state=reloaded
      when: nginxsyntax.rc == 0  and nginxrunning.stat.exists == true   
        
        
    
## when模块
## debug与when连用
##  剧本（Playbooks）
    
    Playbook可以运行多个任务，并提供一些更高级的功能。
    让我们将上述任务移到一本剧本中。在ansible中剧本（playbooks）和角色（roles）都使用Yaml文件定义

### 常用标签

    gather_facts: no
    只要是使用了ansible-playbook，默认情况，第 1 个步骤总是执行 Gathering Facts 这个TASK 
    这个主要收集目标主机的常用信息，如：主机名、内核版本、网卡接口、IP 地址、操作系统版本、CPU、内存、磁盘 等等

    tag标签可以单独使用
    #启动nginx
        - name: start nginx server
          service: name=nginx state=started
          when: 
            - nginxsyntax.rc == 0
            - nginxrunning.stat.exists == false
          tags: updateconfig
          
     ansible-playbook -i hosts site.yml -t updateconfig     
          
    通过debug模块去确认返回结果的数据结构   
    - name: print nginx syntax result
      debug: var=nginxsyntax   
### 定义变量 循环示例

     vars:
        createuser:
          - tomcat
          - www
          - mysql
          
      tasks:
       #利用循环创建多个用户
        - name: create user
          user: name={{item}} state=present
          with_items: "{{createuser}}"

