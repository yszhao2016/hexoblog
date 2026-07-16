---
title: ubuntu24上安装k8s
abbrlink: 581068f7
date: 2026-05-11 19:54:14
tags:
---

# 一、安装前基础操作

设置主机名称
sudo hostnamectl set-hostnam xxx-master

禁用 Swap 分区,加载内核模块

sudo swapoff -a  
sudo sed -i '/swap/ s/^/#/' /etc/fstab

swapon --show  #如果没任何输出，则说明成功

加载overlay、br_netfilter内核模块
sudo tee /etc/modules-load.d/k8s.conf <<EOF
overlay
br_netfilter
EOF

修改内核参数
sudo tee /etc/sysctl.d/kubernetes.conf <<EOF  
net.bridge.bridge-nf-call-ip6tables = 1  
net.bridge.bridge-nf-call-iptables = 1  
net.ip

加载上述内核参数。
sudo sysctl --system



# 二、安装配置 Containerd

sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates


添加阿里云 Docker 镜像源

udo curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"


sudo apt update
sudo apt install containerd.io -y

生成默认配置
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

启用 SystemdCgroup
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml

配置国内镜像源
sudo sed -i "s#registry.k8s.io/pause#registry.cn-hangzhou.aliyuncs.com/google_containers/pause#g" /etc/containerd/config.toml

sudo systemctl restart containerd
sudo systemctl enable containerd


# 三、安装配置 

curl -fsSL https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update


sudo apt install -y kubelet kubeadm kubectl
# 锁定版本，防止意外升级
sudo apt-mark hold kubelet kubeadm kubectl

kubectl命令补全
sudo apt install -y bash-completion
kubectl completion bash | sudo tee /etc/profile.d/kubectl_completion.sh > /dev/null
. /etc/profile.d/kubectl_completion.sh

# 四、Master 初始化集群

 在K8S的Master服务器上生成K8S的安装配置文件，路径/etc/kubernetes/kubeadm.yaml
kubeadm config print init-defaults > /etc/kubernetes/kubeadm.yaml
修改kubeadm.yaml文件 配置参数
```yml
localAPIEndpoint:
  advertiseAddress: 172.18.22.20
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///run/containerd/containerd.sock  #注意修改
  imagePullPolicy: IfNotPresent
  name: test1  #注意修改
  taints: null
#省略一部分
networking:
  dnsDomain: cluster.local
  serviceSubnet: 10.96.0.0/12    #注意修改
  podSubnet: 10.244.0.0/16       #注意修改

```
查看镜像版本
kubeadm config images list

#查看阿里云镜像
kubeadm config images list --image-repository registry.cn-hangzhou.aliyuncs.com/google_containers

初始化k8s
kubeadm init --config=/etc/kubernetes/kubeadm.yaml


给普通用户建一个 “存放登录密钥的文件夹
mkdir -p $HOME/.kube
把root权限的 “集群登录密钥”（admin.conf）复制到普通用户的.kube目录下，并重命名为config
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
把复制后的config文件的 “所有者” 改成当前普通用户（原本复制过来还是 root 权限，普通用户读不了）
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 安装flannel
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

# 五、Worker 节点加入集群

    按照1、2、3步骤，在Worker节点上安装K8S

    mkdir -p $HOME/.kube
    #从master节点 拷贝config文件
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config 
    
    kubeadm join 192.168.100.180:6443 --token abcdef.0123456789abcdef       --discovery-token-ca-cert-hash sha256:1b3aa873cf5301926cb6d7300afbc68ee1a15bd12a303fc34f74a1532ebe504e

#

