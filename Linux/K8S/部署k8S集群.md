---
title: 虚拟机K8s集群搭建
author: 2022-7-28 09:33:02
tags: Kubernets
categories: Kubernets
photos: ["https://tc.chaizz.com/5a5bc3060e3d11ed90590242ac140002.png"]
---

​       

<!--more-->

参考链接

> [安装 Kubernetes 多集群管理工具 - Kuboard v3 | Kuboard](https://www.kuboard.cn/install/v3/install.html)

> [安装Kubernetes(k8s)保姆级教程---无坑版 - Sunzz - 博客园](https://www.cnblogs.com/Sunzz/p/15184167.html)

# 一、前期准备

## 1.1 系统环境

VMware 虚拟机环境下

系统：Ubuntu 22.04 LTS

CPU：2核 | 内存：4G | 硬盘：50G

## 1.2 节点信息

**master：** 192.168.59.130

**node01：** 192.168.59.131

**node02：** 192.168.59.132

确保每个虚拟机唯一节点、网络通畅、开放端口：6443，禁用Swap

## 1.3 关闭防火墙

根据使用的网络模式选择，此处关闭。

```shell
systemctl stop firewalld && systemctl disable firewalld && iptables -F
```

## 1.4 关闭 selinux

安全增强型 Linux（Security-Enhanced Linux）简称 SELinux，它是一个 Linux 内核模块，也是 Linux 的一个安全子系统）关闭是因为可能会导致一些不明的问题。

```shell
sed -i 's/enforcing/disabled/' /etc/selinux/config && setenforce 0
```

## 1.5 关闭Swap

此操作必须执行。

```shell
sed -ri 's/.*swap.*/#&/' /etc/fstab
```

## 1.6 修改hosts文件设置主机名

在每个节点上操作，保证每个虚拟机主机名唯一。

```shell
vi /etc/hosts
# 追加以下内容
192.168.59.130 master.local
192.168.59.131 node01.local
192.168.59.132 node02.local
```

在对应的节点上设置主机名

```shell
# master 节点
hostnamectl set-hostname master.local 

# node01
hostnamectl set-hostname node01.local 

# node02
hostnamectl set-hostname node02.local 
```

## 1.7 允许 iptables 检查桥接流量

```shell
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
```

加载ip_vs内核模块 

不清楚什么意思

```shell
modprobe ip_vs
modprobe ip_vs_rr
modprobe ip_vs_wrr
modprobe ip_vs_sh
modprobe nf_conntrack_ipv4
```

设置开机启动

不清楚什么意思

```shell
cat > /etc/modules-load.d/ip_vs.conf << EOF 
ip_vs
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack_ipv4
EOF
```

# 二、搭建方式

本次使用命令行方式安装，方面理解各个组件的作用。

## 2.1 图形化操作

直接使用[Kuboard-Spray]([GitHub - eip-work/kuboard-spray: 使用图形化的界面离线安装、维护、升级高可用的 K8S 集群](https://github.com/eip-work/kuboard-spray))图形化工具进行搭建。

## 2.2 命令行安装

**以下操作每个节点都需要执行**

### 2.2.1 安装Docker

```shell
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

设置Docker 开机启动

```shell
sudo systemctl enable docker
```

修改Docker的源

```shell
sudo vi /etc/docker/daemon.json
# 修改 registry-mirrors 为如下内容
{
  "registry-mirrors": ["你的阿里云镜像加速地址"]
}
```

```shell
# 重新加载systemctl配置
sudo systemctl daemon-reload

# 重启Docker
sudo systemctl restart docker
```

### 2.2.2 配置kubernetes源

```shell
apt-get update && apt-get install -y apt-transport-https

curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 

cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF  

# 更新apt-get
sudo att-get upadte -y 
```

### 2.2.3 安装kubeadm、kubelet、kubectl

建议安装1.23.6 或以下版本的软件，安装最新版（1.24.+）会有问题，且下面安装kuboard 兼容性未在最新版（1.24.+）验证。

```shell
apt-get install -y kubelet=1.23.6-00  kubeadm=1.23.6-00 kubectl=1.23.6-00
```

设置开机启动

```shell
systemctl enable kubelet
```

**以下操作在master节点执行**

### 2.2.4 master 节点初始化

```shell
kubeadm init \
  --kubernetes-version 1.23.6 \
  --apiserver-advertise-address=192.168.59.130 \
  --service-cidr=10.96.0.0/16 \
  --pod-network-cidr=10.245.0.0/16 \
  --image-repository registry.aliyuncs.com/google_containers

# 参数说明
# kubernetes-version 必须与下载的版本一致。
# apiserver-advertise-address：API 服务器所公布的其正在监听的 IP 地址（本机内网地址）。如果未设置，则使用默认网络接口。
# service-cidr：为服务的虚拟 IP 地址另外指定 IP 地址段， 默认值：10.96.0.0/12。
# pod-network-cidr：指明 pod 网络可以使用的 IP 地址段，如果设置了这个参数，控制平面将会为每一个节点自动分配 CIDRs。
# image-repository：选择用于拉取控制平面镜像的容器仓库，  默认值：k8s.gcr.io。
```

等待拉取镜像，出现以下字样代表master节点初始化成功。

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a Pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  /docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

记住 `kubeadm init` 输出的 `kubeadm join` 命令。 需要使用此命令将子节点加入集群。

### 2.2.5 配置kubectl

要使用非 root 用户可以运行 kubectl，请运行以下命令

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

 `root` 用户，则可以运行：

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

使用kubectl 查看节点信息

```shell
kubectl get nodes
```

目前是只能看到master节点，状态是NotReady，下面是子节点加入后的状态，且网络集群网络联通后的状态。

![](https://tc.chaizz.com/cc6c5fa20e3611ed90590242ac140002.png)

### 2.2.6 子节点加入集群

使用上面初始化后的提示的指令， 分别在各个子节点上执行指令。

```shell
kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

接下来在看看节点状态，下面是子节点加入后的状态，且网络集群网络联通后的状态。

```shell
kubectl get nodes
```

<img title="" src="https://tc.chaizz.com/cc6c5fa20e3611ed90590242ac140002.png" alt="" data-align="inline">

### 2.2.7 安装flannel网络插件

下载插件，[解决raw.githubusercontent.com无法访问的问题](https://blog.csdn.net/weixin_44293949/article/details/121863559)

```shell
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

下载到本地后更改第128行，和上文中的`pod-network-cidr` 一致。

```shell
"Network": "10.245.0.0/16"
```

运行 yaml文件

```shell
kubectl apply -f kube-flannel.yaml
```

查看flannel 结果

```shell
kubectl -n kube-system get pods -o wide
```

![](https://tc.chaizz.com/f296cd5a0e3811ed90590242ac140002.png)

再次查看各个node的状态

```shell
kubectl get nodes
```

![](https://tc.chaizz.com/cc6c5fa20e3611ed90590242ac140002.png)

至此集群方式的Kubernetes 安装完成。

---

# 三、问题

## 问题一：出现 initial timeout of 40s passed。

在一开始部署的时候安装：kubelet、kubeadm、kubectl，没有指定版本，直接使用的最新版（1.24.+），在往上查了半天，找到一篇博客后（地址忘记了）提示说可能是由于版本的问题，然后在重新安装指定版本的kubelet、kubeadm、kubectl 工具，接下来一路畅通。

重新初始化是需要执行 `kubeadm reset` ，此命令是还原由 `kubeadm init` 或 `kubeadm join` 所做的更改。
