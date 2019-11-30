---
title: k8s基本概念
date: 2019-08-04 22:28:50
tags:  k8s

---

## k8s基本对象

- Pod: Pod是最小部署单元，一个Pod有一个或多个容器组成，Pod中容器共享存储和网络，在同一台Docker主机上运行。
- Service
  - Service一个应用服务抽象，定义了一个Pod逻辑集合和访问这个Pod集合的策略。
  - service代理Pod集合对外表现为一个访问入口，分配一个集群IP地址，来自这个IP的请求将负载均衡转发后端Pod中的容器。
  - Service通过Lable Selector选择一个Pod提供服务。
- Volume: 数据卷，共享Pod中容器使用的数据。
- Namespace: 一个命名空间可以看作一个虚拟的集群。将一个k8s分成不同的虚拟集群。在每个虚拟集群中部署一个项目。命名空间将对象逻辑上分配到不同Namespace，可以是不同的项目、用户等区分管理，并设定控制策略，从而实现多租户。命名空间也成为虚拟集群。
- Lable: 标签用于区分对象（比如Pod、Service），键/值对存在；每个对象可以有多个标签，通过标签关联对象。<!--more-->

## 更高层次抽象

- Replicaset: 
  - 下一代Replications Controller。确保任何给定时间指定的Pod副本数量，并提供声明式更新等功能。
  - RC与RS唯一区别就是lable selector支持不同，RS支持新的基于集合的标签，RC仅支持基于等式的标签。

- Deployment:
  - Deployment是一个更高层次的API对象，它管理ReplicaSets和Pod，并提供声明式更新等功能。
  - 官方建议使用Deployment管理ReplicaSet，而不是直接使用ReplicaSets，这意味着可能永远不需要直接操作ReplicaSet对象。
- StatefulSet: StatefulSet适合持久性的应用程序，有唯一个的网络标识符(IP)，持久存储，有序的部署、扩展、删除和滚动更新。
- DaemonSet： DaemonSet确保所有（或一些）节点运行同一个Pod。当节点加入Kuberneter集群中，Pod会被调度到该节点上运行，当节点从集群中移除时，DaemonSet的Pod会被移除。删除DaemonSet会清理它所有创建的Pod。
- Job： 一次性任务，运行完成后Pod销毁，不在重新启动新容器。还可以任务定时运行。比如一个jenkins slave节点，完成项目的构建后，自动销毁；数据库备份；定时发送邮件等等。

## 系统架构即组件功能

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804094559.png)

- Master
  - kubectl：用户管理集群的工具，通过apiserver来管理集群
  - kube-schdeuler： 根据调度算法为新创建的Pod选择一个Node节点
  - kube-apiserver： 集群的统一入口，各组件协调者，以HTTP API 提供接口服务，所有对象资源的增删改查和监听操作都交给APIServer处理后再提交给Etcd存储。
  - kube-controller manager：处理集群中常规后台任务，一个资源对应一个控制器，而ControllerManager就是负责管理这些控制器的。
- Node
  - Kubelet: Kubelet是Master在Node节点上的Agent，管理本机运行容器的生命周期，比如创建容器、Pod挂载数据卷、下载secret、获取容器和节点状态等工作。kubelet将每个Pod转换成一组容器。
  - kube-proxy：负载均衡和网络策略管理，在Node节点实现Pod网络代理，维护网络规则和四层负载均衡工作。
  - docker：运行容器，还可以是rocker/rkt

- 第三方服务
  - etcd: 是一个分布式键值存储系统，保存集群的状态，比如创建Pod、service等对象相关信息

## 集群部署

### 环境规划

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804100110.png)

关闭selinux:

```shell
vi etc/selinux/config

改为：
SELINUX = disabled
```



### 安装Docker

```shell
yum update -y
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum list docker-ce --showduplicates | sort -r
yum install docker-ce-17.12.1.ce

systemctl start docker
systemctl enable docker
```



### 自签TLS证书

### 部署Etcd集群

二进制包下载地址：

```shell
https://github.com/etcd-io/etcd/releases/download/v3.3.13/etcd-v3.3.13-linux-arm64.tar.gz
```



### 部署Flannel网络

- Overlay Network: 覆盖网络，在基础网络上叠加的一种虚拟网络技术模式，该网络中的主机通过虚拟链路连接起来。
- VXLAN: 将源数据包封装到UDP，并使用即使网络的IP/MAC作为外层报文头进行封装，然后在以太网上传输，到达目的地后由隧道端点解封装并将数据发送给目标地址。
- Flannel: 是Overlay网络的一种，也是加源数据包封装在另一种网络包里面进行路由转发和通信，目前已经支持UDP、VXLAN、AWS VPC和GCE路由等数据转发方式。

多主机容器网络通信其他主流方案：隧道方案(Weave、OpenvSwitch)、路由方案(Calico)等。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804124000.png)



### 创建Node节点kubeconfig文件

### 获取k8s二进制包

### 运行Master组件

### 运行Node组件

### 查询集群状态

### 启动一个测试示例

### 部署web UI(Dashboard)





一台linux登陆到另一台linux以及实现文件传输:

```shell
#第一步生成ssh证书
ssh-keygen #在root/.ssh目录下生成公私钥对
#第二步将公钥复制到目标linux服务器上
ssh-copy-id root@192.168.80.11 #输入需要登陆的linux服务器密码授权
#第三步免密码登陆
ssh root@192.168.80.111
#实现文件传输
scp /opt/k8s/bin/  root@192.168.80.111:/opt/k8s/bin
```





## Kubectl命令

- kubectl create : 支持yaml文件和json文件
  - 创建命名空间 kubectl create namespace 命名空间名字
- kubectl get ： 查看当前所有的资源
  - 查询所有的命名空间 kubectl get namespace / ns
  - kubectl get node
  - kubectl get pod
  - kubectl  get all
- kubectl expose: 服务暴露
- kubectl run: 运行一个容器
- kubectl set: 更改一些存在的资源
- kubectl explain : 查看资源的帮助文档，有助于书写yaml，查看一个字段下面有哪些字段
  - kubectl explain pods.spec.containers: 查看在yaml文件中，pod下容器怎么使用
- kubectl edit: 编辑一个资源
  - kubetctl eidt nginx : 会输出该服务的yaml文件，支持修改
- kubectl delete: 删除一个资源
- kubectl rollout
- kubectl rolling-update: 滚动更新
- kubectl scale: 设置副本数量
- kubectl autoscale: 根据资源（主要CPU）自动缩放pod的数量
- kubectl cluster-info: 集群的信息
- kubectl top: 查看节点资源的利用率，需要安装资源收集的插件
- kubectl cordon: 将节点设置为不可调度
- kubectl uncordon: 将节点设置为可调度
- kubectl drain: 
- kubectl taint
- kubectl description: 查看一个资源的描述信息，比如一个容器的启动流程：pull镜像，run镜像，start镜像
- kubectl logs: 查看一个pod的日志，排查容器中的日志
- kubectl attach: 
- kubectl exec 进入容器
  - kubectl exec -it  nginx-5fjdkasjf3132 /bin/bash
- kubectl port-forward: 
- kubectl cp: 拷贝一个文件到容器中
- kubectl apply: 应用一个配置到资源中
- kubectl patch
- kubectl replace: 通过文件或标准输入替换一个资源
- kubectl convert: 转换配置文件
- kubectl label: 更改标签
- kubectl annotate: 添加注释
- kubectl completion: 设置命令自动补全
- kubectl  api-versions: 集群支持的api
- kubectl config : 修改配置文件
- kubectl version: 查看集群组件的版本

可以通过kubectl create --help来查看帮助，以及一些子指令

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804153353.png)



部署一个应用的管理周期：

- 创建一个资源： 

  ```shell
  kubectl run nginx --replicas=3 --labels="app=nginx" --image=nginx:1.10 --port=80
  ```

- 查看资源

  ```shell
  kubectl get all
  kubectl get deploy
  kubectl get svc
  kubectl get pod
  kubectl get pods --show-labels#显示所有pod的标签 -l 指定标签 -o wide 显示详细信息
  kubectl get rs
  ```

- 发布应用

  ```shell
  kubectl expose deployment nginx --port=88 --type=NodePort --target-port=80 --name=nginx-service
  
  kubectl describe service nginx-service
  ```

- 故障排查

  ```shell
  kubectl describe TYPE(pod, deployment, service) 
  kubectl logs nginx-xxx #查看pod日志
  kubectl  exec -it nginx-xxx bash #进入pod
  ```

- 更新

  ```shell
  kubectl set image deployment/nginx nginx:1.11
  kubectl edit deployment/nginx
  ```

- 资源发布管理

  ```shell
  kubectl rollout status deployment/nginx
  kubectl rollout history deployment/nginx
  
  kubectl scale deployment/nginx --replicas=5 #扩容
  ```

- 回滚

  ```shell
  kubectl rollout undo deployment/nginx
  kubectl rollout undo deployment/nginx --to-revision=3
  ```

- 删除

  ```shell
  kubectl delete deployment/nginx
  kubectl delete svc/nginx-service
  ```

   

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804153508.png)



## YAML文件

### 配置文件说明

- 定义配置时，指定最新版本API（当前为v1）（通过`kubectl api-version`查看）；
- 配置文件应该存储在集群之外的版本仓库中。如果需要，可以快速回滚配置、重新创建和恢复；
- 应该使用YAML格式编写配置文件，而不是JSON。尽管这些格式都可以使用，但YAML对用户更加友好；
- 可以将相关对象组合成单个文件，通常会更容易管理；
- 不要没必要的指定默认值，简单和最小配置减少错误；
- 在注释中说明一个对象描述更好维护（注释符号为#）。

### YAML配置文件管理资源

#### 创建deploy的yaml文件：

```yaml
apiVersion: app #api版本，api-version
	kind: Deployment #指定对象的名称，kubectl get --help查看
metadata: #元数据，创建deployment对象的名称,标签和指定命名空间
	name: nginx-deployment 
	namespace: default
	labels:
		web: nginx
spec: #容器的选项
	replicas: 3 #副本数量
	selector: #选择器
		matchLabels: # 匹配标签，控制器匹配pod的标签
			app: nginx
	template: #创建的pod
		metadata: #pod的元数据
			labels:
				app: nginx
		spec: 
			containers: #容器的选项，比如传入的命令，暴露的端口
				- name: nginx #容器名称
					iamge: nginx:1.10 #容器的镜像
					ports: #端口
					- containerPort: 80
```

应用yaml文件创建资源：

```shell
kubectl create -f  nginx-deployment.yaml 
```



 #### 创建service的yaml文件：

```yaml
apiVersion: v1
	kind: Service
metadata:
	name: nginx-service
	labels:
		app: nginx #跟deploy中的标签对上
spec:
	ports:
	- port: 88 #集群的88端口
		targetPort: 80 #容器的80端口
	selector:
		app: nginx 

```

应用yaml文件创建服务：

```shell
kubectl create -f  nginx-service.yaml 
```

## Pod对象(最小部署单元)

### Pod管理

一般不会直接创建pod，而是通过deployment来创建pod。



- 创建/查询/更新/删除

- 资源限制

- 调度约束

- 重启策略

- 健康检查

- 问题定位



### 创建/查询/更新/删除

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: nginx
spec:
	containers: #pod中容器的情况
	- name: nginx #容器名称
		iamge: nginx #容器镜像
```

```shell
kubectl crate -f pod.yaml

kubectl describe pod nginx-pod

#更新资源
kubectl apply -f pod.yaml

#删除
kubectl delete -f  pod.yaml


```

### 资源限制

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: nginx
spec:
	containers:
	- name: nginx
		image: nginx
		resources: #资源
			requests: #保证最小使用的情况
				memory: "64Mi" #内存 62兆
				cpu: "250m" #cpu
			limits: #最大资源使用
				memory: "128Mi"
				cpu: "500m"
				
```

### 调度约束

`Pod.spec.nodeName`:  强制约束pod调度到指定的Node节点上

`Pod.spec.nodeSelector `:通过label-selector机制选择节点

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: nginx
spec:
	#方法一
	#nodeName: 192.169.0.23 # get node得到的name
	#方法二 
	nodeSelector:
		env_role: dev # kubectl label nodes 192.168.0.212 env_role=dev
					 # kubectl describe node 192.168.0.212 来获得该标签dev
	containers:
	- name: nginx
		image: nginx
```



### 重启策略

三种重启策略：

- Always: 当容器停止，总是重建容器，默认策略
- OnFailure: 当容器异常退出（退出状态码非0）时，从重启容器。
- Never: 当容器终止退出，从不重启容器

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: nginx
spec:
	containers:
	- name: nginx
		image: nginx
	restartPolicy: OnFailure
```

### 健康检查

Probe检查容器里面的应用。

提供Probe机制，有以下两种类型

- livenessProbe: 如果检查失败，将杀死容器，根据Pod的restartPolicy来操作
- readinessProbe: 如果检查失败，Kuberneters会把Pod从service endpoints中剔除

Probe支持一下三种检查方法：

- httpGet: 发送HTTP请求，返回200-400范围状态码为成功
- exec: 执行Shell命令返回状态码是0橙色
- tcpSocker: 发起TCP Socket建立成功

```yaml
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: nginx
spec:
	containers:
	- name: nginx
		image: nginx
	- containerPort: 80
    livenessProbe:
		httpGet:
			path: /index.html
			port: 80
```

### 问题定位

```shell
kubectl describe nginx-pod
kubectl logs nginx-xxx
kubectl exec -it nginx-xxx bash
```

## Service

- 网络代理模式
- 服务代理
- 服务发现
- 发布服务

### 网络代理模式

三种代理模式： userspace、iptables、ipvs

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804195131.png)

### Service服务代理

```yaml
apiVersion: v1
kind: Service
metadata:
	name: my-service
spec:
	selector:
		app: Myapp
	ports:
	-name: http
		protocol: TCP
		port: 80
		targetPort: 80
	- name https
		protocol: TCP
		port: 443
		targetPort: 443
```

###  Service服务发现

服务发现支持Service环境变量和DNS两种模式：

- 环境变量

 当一个Pod运行到Node，kubelet会为每个容器添加一组环境变量，Pod容器中程序就快车使用这些环境变量发现Service。

环境变量名格式如下：

​	`{SVCNAME_SERVICE_HOST}`

​	`{SVCNAME_SERVICE_PORT}`

其中服务名和端口号名转为大写，连字符转换为下划线。

进入某个容器：输入`env`就快车看到对应的环境变量。

**限制：**

1） Pod和Service的创建顺序是有要求的，Service必须在Pod创建之前被创建，否则环境变量不会设置到Pod中。

2） Pod只能获取同Namespace种的Service环境变量。

- DNS

  DNS服务监听Kuberneters API，为每一个Service创建DNS记录用于域名解析。这样Pod中就可以通过DNS域名获取Service的访问地址。

#### kube-DNS工作原理

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804192016.png)



### Service发布服务

- Cluster IP:

  分配一个内部集群IP地址，只能在集群内部访问（同Namespace内的Pod），默认ServiceType

- NodePort:

  - 分配一个内部集群IP地址，并在每个节点上启用一个端口来暴露服务，可以在集群外部访问。
  - 访问地址：<NodeIP>:<NodePort>

- LoadBalancer:

  - 分配一个内部集群IP地址，并在每个节点上启动一个端口来暴露服务。
  - 除此之外，Kuberneter会请求底层云平台上的负载均衡，将每个Node([NodeIP]:[NodePort])作为后端添加进去。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190804194816.png)



```yaml
apiVersion: v1
	kind: Service
metadata:
	name: nginx-service
	labels:
		app: nginx #跟deploy中的标签对上
spec:
	ports:
	- port: 80 #集群的88端口
		targetPort: 80 #容器的80端口
	selector:
		app: nginx 
	type: NodePort #暴露端口，随机生成的
```





学习视频：

[一天入门Kuberneters]: https://ke.qq.com/course/366778?taid=2773350577707194	"一天入门Kuberneters"

