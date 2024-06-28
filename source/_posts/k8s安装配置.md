---
title: k8s安装配置
abbrlink: 5951e8f7
date: 2024-05-16 19:58:36
tags:k8s
---

# 一、redhat/openEluer 22/Centos 系统 安装k8s

## 1.1 更新源

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
编辑替换  %s/$releasever/7/g

设置仓库，需要安装所需的软件包。yum-utils 提供了 yum-config-manager ，
并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2

yum install -y yum-utils device-mapper-persistent-data lvm2

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
编辑替换  %s/$releasever/7/g


## 1.2 安装 docker

yum install docker-ce

## 1.3 新增k8s源及安装
新增k8s源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
EOF


yum install kubeadm kubelet kubectl 


# 二、ubuntu 系统安装 k8s

 sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
 
 curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
  
 sudo apt-add-repository "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main"
  
 sudo apt-get update
  
 
  vi /etc/apt/sources.list

 deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main

 apt-get update
 sudo apt update
 
 #sudo apt install -y kubelet=1.26.1-00 kubeadm=1.26.1-00 kubectl=1.26.1-00
 
 sudo apt install kubelet kubeadm kubectl
 sudo apt-mark hold kubelet kubeadm kubectl

# 三、必要的系统设置

## 3.1 关闭 selinux  关闭防火墙【暂时】（redhat系统）
零时关闭
   setenforce 0  
   
永久关闭
       vi /etc/selinux/config
       将参数修改为SELINUX=disabled
   
关闭防火墙

   systemctl stop iptables
   
   systemctl stop firewalld
   
   systemct stop ufw

## 3.2 禁用交换分区  
    
   swapoff -a 
   
   /etc/fstab 文件下永久关闭 
   打开/etc/fstab注释掉swap那一行
   
   sed -i 's/.*swap.*/#&/' /etc/fstab  

## 3.3 开启端口转发   

   vim  /proc/sys/net/ipv4/ip_forward
   sysctl -w net.ipv4.ip_forward=1
   
## 3.4 修改内核参数 

cat <<EOF >  /etc/sysctl.d/k8s.conf  
net.bridge.bridge-nf-call-ip6tables = 1  
net.bridge.bridge-nf-call-iptables = 1  
EOF  
sysctl --system 

## 3.5 设置时区更新时间

     timedatectl  查看时区
     
     设置时区
     timedatectl set-timezone Asia/Shanghai
     
## 配置host

    vim /etc/hosts
    
     
 
# 三、master 节点配置

 ## 3.1 配置项 解释 
 
 自定义镜像进行初始化(推荐)
 使用自定义kubernets镜像仓库进行初始化
 
 apiserver-advertise-address: 配置 apiserver ip  本机ip
                             指明用 Master 的哪个 interface 与 Cluster 的其他节点通信。
                             如果 Master 有多个 interface，建议明确指定，如果不指定，kubeadm 会自动选择有默认网关的 interface。
                             这里的ip为master节点ip，记得更换
                                 
 apiserver-bind-port       : 配置 apiserver 端口 默认 6443
 
 control-plane-endpoint    : 配置 控制端   ip  默认本机ip
                             cluster-endpoint 是映射到该 IP 的自定义 DNS 名称，这里配置hosts映射：192.168.0.113   cluster-endpoint。
                             这将允许你将 --control-plane-endpoint=cluster-endpoint 传递给 kubeadm init，并将相同的 DNS 名称传递给 kubeadm join。 
                             稍后你可以修改 cluster-endpoint 以指向高可用性方案中的负载均衡器的地址。
 
 service-cidr               这个参数指定了 Kubernetes 集群中服务（Service）的 IP 地址范围。在该示例中，服务 IP 地址将从 10.119.0.0 开始分配。
                                  
 service-dns-domain         这个参数指定了 Kubernetes 集群中服务的 DNS 域名。在该示例中，服务的 DNS 域名将使用 "cluster.local"。
                            
 pod-network-cidr  :    这个参数指定了 Kubernetes 集群中 Pod 的 IP 地址范围。在该示例中，Pod 的 IP 地址将从 10.120.0.0 开始分配。 
                        指定 Pod 网络的范围。Kubernetes 支持多种网络方案，而且不同网络方案对  
                        –pod-network-cidr有自己的要求，这里设置为10.244.0.0/16 是因为我们将使用 flannel 网络方案
                        必须设置成这个 CIDR。

 image-repository           这个用于指定从什么位置来拉取镜像（1.13版本才有的），默认值是k8s.gcr.io，
                            我们将其指定为国内镜像地址：registry.aliyuncs.com/google_containers
                                                        
 kubernetes-version        指定kubenets版本号，默认值是stable-1，会导致从https://dl.k8s.io/release/stable-1.txt下载最新的版本号，
                           我们可以将其指定为固定版本（v1.22.1）来跳过网络请求
                           
                           
 ## 3.2配置示例：
 
 kubeadm init \
 --apiserver-advertise-address 192.168.240.11 \
 --apiserver-bind-port 6443 \
 --control-plane-endpoint 192.168.240.11 \
 --service-cidr=10.119.0.0/16 \
 --service-dns-domain="cluster.local" \
 --pod-network-cidr=10.120.0.0/16 \
 --image-repository registry.aliyuncs.com/google_containers 
    
 kubeadm init \
  --apiserver-advertise-address=192.168.100.203 \
  --image-repository registry.aliyuncs.com/google_containers \
  --service-cidr=10.119.0.0/16 \
  --pod-network-cidr=10.120.0.0/16 \
  --kubernetes-version v1.28.2
     
 kubeadm init \
   --apiserver-advertise-address=192.168.31.200 \
   --image-repository registry.aliyuncs.com/google_containers \
   --kubernetes-version v1.18.0 \
   --service-cidr=10.96.0.0/12 \
   --pod-network-cidr=10.244.0.0/16
   
## 3.3 配置 kubectl 

   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   echo 'export KUBECONFIG=$HOME/.kube/config' >> $HOME/.bashrc
   source ~/.bashrc

## 3.3 配置网络

   kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

   如果被墙，可以直接保存下列文件进行安装
    
   kubectl apply -f kube-flannel.yaml


# 四、node 节点配置

## 4.1 配置文件处理

 mkdir -p $HOME/.kube
 
 复制master节点的 /etc/kubernetes/admin.conf 到 node节点 $HOME/.kube/config
 
 sudo  $(id -u):$(id -g) $HOME/.kube/config
 
 echo 'export KUBECONFIG=$HOME/.kube/config' >> $HOME/.bashrc
 
 source ~/.bashrc
                      
## 4.2 加入节点
    
   可以master 节点执行  kubeadm token create --print-join-command 获取加入节点命令
   
   kubeadm join 172.17.131.4:6443 --token decqn7.lwcygbjmzof0xny3  \
   --discovery-token-ca-cert-hash sha256:8f0e8d2c04e38c7aa291d8b47be2bd8a5127bf1071263e01bdd5602ab331a01d
    


# 五、相关命令

 如何生成 Kubeadm Join 命令？
 您可以使用 command 生成 join 命令。kubeadm token create --print-join-command
 
 
 重置节点（清除kubeadm信息）
 kubeadm reset


 查看 pod 节点
 kubectl get nodes


 删除的节点
 kubectl delete node <node_name>

于官网镜像在国内访问不稳定，会出现访问失败。
使用国内镜像进行替换下载，在此pause使用3.9会导致集群无法初始化，还需要下载pause:3.6
以下命令在每台服务器上执行
使用自定义镜像仓库拉取镜像
kubeadm config images pull  \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.28.2


# 六 报错


 报错如下
 "open /run/systemd/resolve/resolv.conf: no such file or directory" pod="kubelet
 解决：
 yum install systemd-resolved 解决


报错如下：
combined from similar events): Failed to create pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox \&quot;4d992cde6a671b8fa3264a90f5f11aa01d1713376abf0cdf9a4e27ec82860185\&quot;: plugin type=\&quot;flannel\&quot; failed (add): loadFlannelSubnetEnv 
failed: open /run/flannel/subnet.env: no such file or directory


解决：
配置 /run/flannel/subnet.env
pod 网络信息 

FLANNEL_NETWORK=10.120.0.0/16
FLANNEL_SUBNET=10.120.0.1/24
FLANNEL_MTU=1450
FLANNEL_IPMASQ=true

报错如下：
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher


 解决：
sysctl -w net.ipv4.ip_forward=1



报错如下：

 NotReady message:Network plugin returns error: cni plugin not initialized
 to get sandbox image \"registry.k8s.i
 
 evel=info msg="Stop CRI service"
 024-05-31T02:22:27.294384683Z" level=info msg="stop pulling image registry.k8s.io/pause:3.8: active requests=1, bytes>
 024-05-31T02:22:27.294515739Z" level=info msg="trying next host" error="failed to do request: Head \"https://us-west2> 

 
解决

containerd config default > /etc/containerd/config.toml

修改
sandbox_image = "registry.aliyuncs.com/google_containers/pause:3.9"

systemctl restart containerd


mkdir /etc/containerd
containerd config default > /etc/containerd/config.toml
sed -i '/sandbox_image/s#registry.k8s.io/pause:3.8#registry.aliyuncs.com/google_containers/pause:3.9#g' /etc/containerd/config.toml
sed -i '/SystemdCgroup/s/false/true/' /etc/containerd/config.toml
systemctl restart containerd
    
 

发现不了节点报错
https://blog.csdn.net/qq_32264301/article/details/125486521


 error="rpc error: code = Unavailable desc = error reading from server: EOF" module=libcontainerd namespace=moby
 


报错
Error registering network: failed to acquire lease: subnet "10.244.0.0/16" 
specified in the flannel net config doesn't contain "10.120.0.0/24" PodCIDR of the "ubuntuutoootest" node 


修改 kube-flannel.yml

net-conf.json: |
    {
      "Network": "10.120.0.0/16",
      "EnableNFTables": false,
      "Backend": {
        "Type": "vxlan"
      }
    }
---




# k8组件
k8s的主要组件，以及它们主要是用来干什么的：

3.1 「主节点（Master）」
主节点负责管理集群的整体状态和控制工作节点的操作。它包括以下组件：

「API Server：」 提供了K8s API的入口，用于与Kubernetes进行交互。
「Controller Manager：」 负责监控系统状态，确保实际状态符合期望状态。
「Scheduler：」 负责将Pod调度到工作节点上运行。

3.2 「工作节点（Node）」
工作节点是集群中的计算资源，用于运行容器。每个工作节点包括以下组件：

「Kubelet：」 负责与主节点通信，确保在节点上运行所需的Pod。
「Container Runtime：」 用于启动和管理容器的软件，如Docker。
「Kube Proxy：」 负责在节点上实现Service的网络代理。


etcd：一款开源软件。提供可靠的分布式数据存储服务，用于持久化存储K8s集群的配置和状态

apiservice：用户程序（如kubectl）、K8s其它组件之间通信的接口。K8s其它组件之间不直接通信，而是通过API server通信的。这一点在上图的连接中可以体现，例如，只有API server连接了etcd，即其它组件更新K8s集群的状态时，只能通过API server读写etcd中的数据。

Scheduler：排程组件，为用户应用的每一可部署组件分配工作结点。

controller-manager：执行集群级别的功能，如复制组件、追踪工作结点状态、处理结点失败等。Controller Manager组件是由多个控制器组成的，其中很多控制器是按K8s的资源类型划分的，如Replication Manager（管理ReplicationController 资源），ReplicaSet Controller，PersistentVolume controller。

kube-proxy：在应用组件间负载均衡网络流量。

kubelet：管理工作结点上的容器。

Contriner runtime Docker， rkt等实际运行容器的组件


报错




报错



