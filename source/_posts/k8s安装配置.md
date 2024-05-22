---
title: k8s安装配置
date: 2024-05-16 19:58:36
tags:
---



k8s的主要组件，以及它们主要是用来干什么的：
etcd：一款开源软件。提供可靠的分布式数据存储服务，用于持久化存储K8s集群的配置和状态

apiservice：用户程序（如kubectl）、K8s其它组件之间通信的接口。K8s其它组件之间不直接通信，而是通过API server通信的。这一点在上图的连接中可以体现，例如，只有API server连接了etcd，即其它组件更新K8s集群的状态时，只能通过API server读写etcd中的数据。

Scheduler：排程组件，为用户应用的每一可部署组件分配工作结点。

controller-manager：执行集群级别的功能，如复制组件、追踪工作结点状态、处理结点失败等。Controller Manager组件是由多个控制器组成的，其中很多控制器是按K8s的资源类型划分的，如Replication Manager（管理ReplicationController 资源），ReplicaSet Controller，PersistentVolume controller。

kube-proxy：在应用组件间负载均衡网络流量。

kubelet：管理工作结点上的容器。

Contriner runtime Docker， rkt等实际运行容器的组件



openEluer 22 系统
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
编辑替换  %s/$releasever/7/g

yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
编辑替换  %s/$releasever/7/g

yum install docker-ce

新增k8s源
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=0
EOF




 1129   kubeadm join 192.168.100.235:6443 --token kxbaap.0ipdqdb63vxbl7q6 --discovery-token-ca-cert-hash sha256:a5a32ab2ef97a8457d27ddef66ca90afa24151ca6b0eff2b499028facc4ce330
 2920  kubectl get nodes
 2925  yum install -y kubelet kubeadm kubectl docker
 2943  kubelet status
 2944  systemctl status kubelet
 2945  vim /var/lib/kubelet/config.yaml
 2946  vim /etc/kubernetes/pki/ca.crt
 
 
 timedatectl  查看时区
 
 设置时区
 timedatectl set-timezone Asia/Shanghai
 
 禁用swap
 sudo swapoff -a

 永久关闭 
 sudo vi /etc/fstab
 
 
 添加/etc/hosts
 
 sudo apt-update
 sudo apt-upgrade
 sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
 
 curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
  
 sudo apt-add-repository "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main"
  
 sudo apt-get update
  
  ##用这个
  vi /etc/apt/sources.list
 ##添加
 deb https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main
 ##更新
 apt-get update
 sudo apt update
 #sudo apt install -y kubelet=1.26.1-00 kubeadm=1.26.1-00 kubectl=1.26.1-00
 sudo apt install kubelet kubeadm kubectl
 sudo apt-mark hold kubelet kubeadm kubectl
 
 

 
 kubeadm init \
 --apiserver-advertise-address=192.168.100.203 \
 --image-repository registry.aliyuncs.com/google_containers \
 --service-cidr=10.119.0.0/16 \
 --pod-network-cidr=10.120.0.0/16 \
 --kubernetes-version v1.28.2
 
 
 自定义镜像进行初始化(推荐)
 # 使用自定义kubernets镜像仓库进行初始化
 kubeadm init \
 --apiserver-advertise-address 192.168.240.11 \
 --apiserver-bind-port 6443 \
 --control-plane-endpoint 192.168.240.11 \
 --service-cidr=10.119.0.0/16 \
 --service-dns-domain="cluster.local" \
 --pod-network-cidr=10.120.0.0/16 \
 --image-repository registry.aliyuncs.com/google_containers 

 
 
 kubeadm init \\
   --apiserver-advertise-address=192.168.31.200 \\
   --image-repository registry.aliyuncs.com/google_containers \\
   --kubernetes-version v1.18.0 \\
   --service-cidr=10.96.0.0/12 \\
   --pod-network-cidr=10.244.0.0/16
 

 
 
 
 root@ubuntuutoootest:/etc/kubernetes/manifests# kubeadm init  --apiserver-advertise-address=192.168.100.203  --image-repository registry.aliyuncs.com/google_containers  --service-cidr=10.119.0.0/16  --pod-network-cidr=10.120.0.0/16  --kubernetes-version v1.28.2
 [init] Using Kubernetes version: v1.28.2
 [preflight] Running pre-flight checks
 [preflight] Pulling images required for setting up a Kubernetes cluster
 [preflight] This might take a minute or two, depending on the speed of your internet connection
 [preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
 W0520 10:34:46.155015   73243 checks.go:835] detected that the sandbox image "registry.aliyuncs.com/google_containers/pause:3.8" of the container runtime is inconsistent with that used by kubeadm. It is recommended that using "registry.aliyuncs.com/google_containers/pause:3.9" as the CRI sandbox image.
 [certs] Using certificateDir folder "/etc/kubernetes/pki"
 [certs] Generating "ca" certificate and key
 [certs] Generating "apiserver" certificate and key
 [certs] apiserver serving cert is signed for DNS names [kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local ubuntuutoootest] and IPs [10.119.0.1 192.168.100.203]
 [certs] Generating "apiserver-kubelet-client" certificate and key
 [certs] Generating "front-proxy-ca" certificate and key
 [certs] Generating "front-proxy-client" certificate and key
 [certs] Generating "etcd/ca" certificate and key
 [certs] Generating "etcd/server" certificate and key
 [certs] etcd/server serving cert is signed for DNS names [localhost ubuntuutoootest] and IPs [192.168.100.203 127.0.0.1 ::1]
 [certs] Generating "etcd/peer" certificate and key
 [certs] etcd/peer serving cert is signed for DNS names [localhost ubuntuutoootest] and IPs [192.168.100.203 127.0.0.1 ::1]
 [certs] Generating "etcd/healthcheck-client" certificate and key
 [certs] Generating "apiserver-etcd-client" certificate and key
 [certs] Generating "sa" key and public key
 [kubeconfig] Using kubeconfig folder "/etc/kubernetes"
 [kubeconfig] Writing "admin.conf" kubeconfig file
 [kubeconfig] Writing "kubelet.conf" kubeconfig file
 [kubeconfig] Writing "controller-manager.conf" kubeconfig file
 [kubeconfig] Writing "scheduler.conf" kubeconfig file
 [etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
 [control-plane] Using manifest folder "/etc/kubernetes/manifests"
 [control-plane] Creating static Pod manifest for "kube-apiserver"
 [control-plane] Creating static Pod manifest for "kube-controller-manager"
 [control-plane] Creating static Pod manifest for "kube-scheduler"
 [kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
 [kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
 [kubelet-start] Starting the kubelet
 [wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
 
 
 [apiclient] All control plane components are healthy after 9.506687 seconds
 [upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
 [kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
 [upload-certs] Skipping phase. Please see --upload-certs
 [mark-control-plane] Marking the node ubuntuutoootest as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
 [mark-control-plane] Marking the node ubuntuutoootest as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
 [bootstrap-token] Using token: rta63y.nv3u09axumelodas
 [bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
 [bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
 [bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
 [bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
 [bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
 [bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
 [kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
 [addons] Applied essential addon: CoreDNS
 [addons] Applied essential addon: kube-proxy
 
 Your Kubernetes control-plane has initialized successfully!
 
 To start using your cluster, you need to run the following as a regular user:
 
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
 
 Alternatively, if you are the root user, you can run:
 
   export KUBECONFIG=/etc/kubernetes/admin.conf
 
 You should now deploy a pod network to the cluster.
 Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
   https://kubernetes.io/docs/concepts/cluster-administration/addons/
 
 Then you can join any number of worker nodes by running the following on each as root:
 
 kubeadm join 192.168.100.203:6443 --token rta63y.nv3u09axumelodas \
 	--discovery-token-ca-cert-hash sha256:37488f7329d00c3a6c9d29bf6924f93e563bd113f3bce8a4df76c6dd96f6d5df 

 
 
 
kubectl get nodes

 
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

 
 如何生成 Kubeadm Join 命令？
 您可以使用 command 生成 join 命令。kubeadm token create --print-join-command
 
 
 # 重置节点（清除kubeadm信息）
 kubeadm reset
 



于官网镜像在国内访问不稳定，会出现访问失败。
使用国内镜像进行替换下载，在此pause使用3.9会导致集群无法初始化，还需要下载pause:3.6
以下命令在每台服务器上执行
使用自定义镜像仓库拉取镜像

kubeadm config images pull \
--image-repository registry.aliyuncs.com/google_containers


kubeadm config images pull  \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.28.2




报错

"open /run/systemd/resolve/resolv.conf: no such file or directory" pod="kubelet

yum install systemd-resolved 解决


报错
[preflight] Running pre-flight checks
error execution phase preflight: [preflight] Some fatal errors occurred:
	[ERROR FileContent--proc-sys-net-ipv4-ip_forward]: /proc/sys/net/ipv4/ip_forward contents are not set to 1
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher



报错

combined from similar events): Failed to create pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox \&quot;4d992cde6a671b8fa3264a90f5f11aa01d1713376abf0cdf9a4e27ec82860185\&quot;: plugin type=\&quot;flannel\&quot; failed (add): loadFlannelSubnetEnv failed: open /run/flannel/subnet.env: no such file or directory



sysctl -w net.ipv4.ip_forward=1
