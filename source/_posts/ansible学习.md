#ansible学习


## 安装

    yum install -y epel-release
    yum install -y ansible
    #ansible配置文件：
    /etc/ansible/ansible.cfg
    /etc/ansible/hosts
    生成秘钥：
    ssh-keygen -t rsa  #在/root/.ssh/目录下生成秘钥
    #被控端：(复制主机端公钥到被控端)
    ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.7.99 
    ssh-copy-id  被控端ip
   
## 配置/etc/ansible/hosts



##ansible的copy模块


