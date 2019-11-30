---
title: 使用kubeadm快速部署一个k8s集群
date: 2019-08-18 22:28:50
tags:  k8s
---



> #### 作者：李振良



kubeadm是官方社区推出的一个用于快速部署kubernetes集群的工具。<!--more-->

这个工具能通过两条指令完成一个kubernetes集群的部署：

```shell
# 创建一个 Master 节点
$ kubeadm init

# 将一个 Node 节点加入到当前集群中
$ kubeadm join <Master节点的IP和端口 >
```

## 1. 安装要求

在开始之前，部署Kubernetes集群机器需要满足以下几个条件：

- 一台或多台机器，操作系统 CentOS7.x-86_x64
- 硬件配置：2GB或更多RAM，2个CPU或更多CPU，硬盘30GB或更多
- 集群中所有机器之间网络互通
- 可以访问外网，需要拉取镜像
- 禁止swap分区

## 2. 学习目标

1. 在所有节点上安装Docker和kubeadm
2. 部署Kubernetes Master
3. 部署容器网络插件
4. 部署 Kubernetes Node，将节点加入Kubernetes集群中
5. 部署Dashboard Web页面，可视化查看Kubernetes资源

## 3. 准备环境

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190805085254.png)



​									    Kubernetes 架构图

```shell
关闭防火墙：
$ systemctl stop firewalld
$ systemctl disable firewalld

关闭selinux：
$ sed -i 's/enforcing/disabled/' /etc/selinux/config 
$ setenforce 0

关闭swap：
$ swapoff -a  $ 临时
$ vim /etc/fstab  $ 永久

添加主机名与IP对应关系（记得设置主机名）：
$ cat /etc/hosts
192.168.31.63 k8s-master
192.168.31.65 k8s-node1
192.168.31.66 k8s-node2


104.211.35.206 k8s-master
52.168.8.162 k8s-node

将桥接的IPv4流量传递到iptables的链：
$ cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
$ sysctl --system
```

## 4. 所有节点安装Docker/kubeadm/kubelet

Kubernetes默认CRI（容器运行时）为Docker，因此先安装Docker。

### 4.1 安装Docker

```
$ wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
$ yum -y install docker-ce-18.06.1.ce-3.el7
$ systemctl enable docker && systemctl start docker
$ docker --version
Docker version 18.06.1-ce, build e68fc7a
```

### 4.2 添加阿里云YUM软件源

```
$ cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

### 4.3 安装kubeadm，kubelet和kubectl

由于版本更新频繁，这里指定版本号部署：

```
$ yum install -y kubelet-1.14.0 kubeadm-1.14.0 kubectl-1.14.0
$ systemctl enable kubelet
```

## 5. 部署Kubernetes Master

在104.211.35.206（Master）执行。

```
$ kubeadm init \
  --apiserver-advertise-address=104.211.35.206 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.14.0 \
  --service-cidr=10.1.0.0/16 \
  --pod-network-cidr=10.244.0.0/16
```

由于默认拉取镜像地址k8s.gcr.io国内无法访问，这里指定阿里云镜像仓库地址。

使用kubectl工具：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
$ kubectl get nodes
```

## 6. 安装Pod网络插件（CNI）

```
$ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/a70459be0084506e4ec919aa1c114638878db11b/Documentation/kube-flannel.yml
```

确保能够访问到quay.io这个registery。

如果下载失败，可以改成这个镜像地址：lizhenliang/flannel:v0.11.0-amd64

## 7. 加入Kubernetes Node

在192.168.31.65/66（Node）执行。

向集群添加新节点，执行在kubeadm init输出的kubeadm join命令：

```
$  kubeadm join 13.125.214.112:6443 --token phleld.5p1reb2q7b3ovhyo \
    --discovery-token-ca-cert-hash sha256:8896989365af03eaee3117a44172140eb10dfd0704affe42b22705871aa23edc
```

可以通过下面的命令来获取将节点node添加到集群的命令：

```shell
kubeadm token create --print-join-command 
```

## 8. 测试kubernetes集群

在Kubernetes集群中创建一个pod，验证是否正常运行：

```
$ kubectl create deployment nginx --image=nginx
$ kubectl expose deployment nginx --port=80 --type=NodePort
$ kubectl get pod,svc
```

访问地址：http://NodeIP:Port  

## 9. 部署 Dashboard

```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

默认镜像国内无法访问，修改镜像地址为： lizhenliang/kubernetes-dashboard-amd64:v1.10.1

默认Dashboard只能集群内部访问，修改Service为NodePort类型，暴露到外部：

```
kind: Service
apiVersion: v1
metadata:
  labels:
    k8s-app: kubernetes-dashboard
  name: kubernetes-dashboard
  namespace: kube-system
spec:
  type: NodePort
  ports:
    - port: 443
      targetPort: 8443
      nodePort: 30001
  selector:
    k8s-app: kubernetes-dashboard
```
```
$ kubectl apply -f kubernetes-dashboard.yaml
```
访问地址：http://NodeIP:30001

创建service account并绑定默认cluster-admin管理员集群角色：

```
$ kubectl create serviceaccount dashboard-admin-1 -n kube-system
$ kubectl create clusterrolebinding dashboard-admin-1 --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
$ kubectl describe secrets -n kube-system $(kubectl -n kube-system get secret | awk '/dashboard-admin/{print $1}')
```
使用输出的token登录Dashboard。









