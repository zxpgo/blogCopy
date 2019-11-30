---
title: k8s中jenkins的部署以及jenkins的配置和使用(C++)
date: 2019-08-06 11:44:14
tags: k8s
mathjax: true
---

转自： https://www.kancloud.cn/huyipow/kubernetes/716441

本文将讨论和探索两个令人惊奇和相当有趣的技术。一个是Jenkins，一个流行的持续集成/发布的工具，另一个是Kubernetes，一个流行的容器编排引擎。<!--more-->

持续构建与发布是我们日常工作中必不可少的一个步骤，目前大多公司都采用 Jenkins 集群来搭建符合需求的 CI/CD 流程，然而传统的 Jenkins Slave 一主多从方式会存在一些痛点，比如：主 Master 发生单点故障时，整个流程都不可用了；每个 Slave 的配置环境不一样，来完成不同语言的编译打包等操作，但是这些差异化的配置导致管理起来非常不方便，维护起来也是比较费劲；资源分配不均衡，有的 Slave 要运行的 job 出现排队等待，而有的 Slave 处于空闲状态；最后资源有浪费，每台 Slave 可能是实体机或者 VM，当 Slave 处于空闲状态时，也不会完全释放掉资源。

提到基于Kubernete的CI/CD，可以使用的工具有很多，比如Jenkins、Gitlab CI已经新兴的drone之类的，我们这里会使用大家最为熟悉的Jenins来做CI/CD的工具。

基于 Kubernetes 搭建 Jenkins 集群的简单示意图

![](https://www.qikqiak.com/img/posts/k8s-jenkins-slave.png)

从图上可以看到 Jenkins Master 和 Jenkins Slave 以 Pod 形式运行在 Kubernetes 集群的 Node 上，Master 运行在其中一个节点，并且将其配置数据存储到一个 Volume 上去，Slave 运行在各个节点上，并且它不是一直处于运行状态，它会按照需求动态的创建并自动删除。

这种方式的工作流程大致为：当 Jenkins Master 接受到 Build 请求时，会根据配置的 Label 动态创建一个运行在 Pod 中的 Jenkins Slave 并注册到 Master 上，当运行完 Job 后，这个 Slave 会被注销并且这个 Pod 也会自动删除，恢复到最初状态。

## 安装

为了方便演示，我们把本节课所有的对象资源都放置在一个名为 kube-ops 的 namespace 下面，所以我们需要添加创建一个 namespace：

```shell
kubectl create namespace kube-jenkins
```

另外我们这里还需要使用到一个拥有相关权限的 serviceAccount：jenkins2，我们这里只是给 jenkins 赋予了一些必要的权限，当然如果你对 serviceAccount 的权限不是很熟悉的话，我们给这个 sa 绑定一个 cluster-admin 的集群角色权限也是可以的，当然这样具有一定的安全风险：（jenkins-rbac.yaml）

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins2
  namespace: kube-jenkins

---

kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: jenkins2
  namespace: kube-jenkins
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["create","delete","get","list","patch","update","watch"]
  - apiGroups: [""]
    resources: ["pods/exec"]
    verbs: ["create","delete","get","list","patch","update","watch"]
  - apiGroups: [""]
    resources: ["deployments"]
    verbs: ["create", "delete", "get"]
  - apiGroups: [""]
    resources: ["pods/log"]
    verbs: ["get","list","watch"]
  - apiGroups: [""]
    resources: ["secrets"]
    verbs: ["get"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: jenkins2
  namespace: kube-jenkins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: jenkins2
subjects:
  - kind: ServiceAccount
    name: jenkins2
    namespace: kube-jenkins

```

创建 rbac 相关的资源对象：

```shell
kubectl create -f jenkins-rbac.yaml
```

既然要基于Kubernetes来做CI/CD，当然我们这里需要将 Jenkins 安装到 Kubernetes 集群当中，新建一个 Deployment：(jenkins-deployment.yaml)：

deployment.yaml

```yaml
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: jenkins2
  namespace: kube-jenkins
spec:
  template:
    metadata:
      labels:
        app: jenkins2
    spec:
      terminationGracePeriodSeconds: 10
      serviceAccountName: jenkins2
      containers:
      - name: jenkins
        image: jenkins/jenkins:lts
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
          name: web
          protocol: TCP
        - containerPort: 50000
          name: agent
          protocol: TCP
        resources:
          limits:
            cpu: 2000m
            memory: 4Gi
          requests:
            cpu: 1000m
            memory: 2Gi
        livenessProbe:
          httpGet:
            path: /login
            port: 8080
          initialDelaySeconds: 60
          timeoutSeconds: 5
          failureThreshold: 12
        readinessProbe:
          httpGet:
            path: /login
            port: 8080
          initialDelaySeconds: 60
          timeoutSeconds: 5
          failureThreshold: 12
        env:
        - name: LIMITS_MEMORY
          valueFrom:
            resourceFieldRef:
              resource: limits.memory
              divisor: 1Mi
        - name: JAVA_OPTS
          value: -Xmx$(LIMITS_MEMORY)m -XshowSettings:vm -Dhudson.slaves.NodeProvisioner.initialDelay=0 -Dhudson.slaves.NodeProvisioner.MARGIN=50 -Dhudson.slaves.NodeProvisioner.MARGIN0=0.85 -Duser.timezone=Asia/Shanghai
      securityContext:
        fsGroup: 1000

---
apiVersion: v1
kind: Service
metadata:
  name: jenkins2
  namespace: kube-jenkins
  labels:
    app: jenkins2
spec:
  selector:
    app: jenkins2
  type: NodePort
  ports:
  - name: web
    port: 8080
    nodePort: 30002
    targetPort: web
  - name: agent
    port: 50000
    nodePort: 30003
    targetPort: agent
```

一切准备的资源准备好过后，我们直接创建 Jenkins 服务：

```shell
kubectl create -f jenkins-deployment.yaml
```

创建完成后，要去拉取镜像可能需要等待一会儿，然后我们查看下 Pod 的状态。

在部署jenkins时，我们指定的暴露服务的方式是NodePort，所以后随机给一个端口，通过node的IP加端口就可以访问jenkins网站了。

## 配置

在kubernetes中安装好jenkins，然后就是在jenkins中配置kubernetes。

首先，在jenkins中安装插件kubernetes。

然后，Manage Jenkins -> Configure System，底部点击添加一个云，然后选择kubernetes。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190811185600.png)

需要设置的Kubernetes地址，Kubernetes命名空间，以及jenkins地址。注意这里的jenkins url一定主要注意，是**集群IP**，而不是公网上的IP，而对于在云上部署的，这一个jenkins url是**k8s中jenkins的集群IP（Cluster IP）**。填写好kubernetes的信息后，点击链接测试，如果出现Connection test successful，表示kubernetes链接成功。

## 使用

在jenkins中，创建一个pipeline。下面是一个pipeline的模板：

```yaml
podTemplate(
    containers: [containerTemplate(name: 'maven', image: 'zxpwin/kubectl-centos', ttyEnabled: true, command: 'cat')], 
    volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')],
	serviceAccount: 'jenkins2',
	namespace: 'kube-jenkins'
){
     node(POD_LABEL) {
        container('maven') {
            stage('Clone') {
                checkout ([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [],submoduleCfg: [], userRemoteConfigs: [[credentialsId:  '*************************',url: 'https://github.com**********']]]) 
            }
                  
            stage('Build'){    
               sh 'mvn package'
            }
            stage('Build Docker'){
                /*构建镜像*/
                sh 'docker build -t cicd_test_docker .'
                /*推送镜像*/
               sh 'docker tag cicd_test_docker zxpwin/cicd-test-docker'
               withCredentials([usernamePassword(credentialsId: 'dockerHubID', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                   sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
                   sh "docker push zxpwin/cicd-test-docker"
               }
            }
            stage('Deploy'){
            	/*部署*/
                sh 'kubectl create deployment cicd --image=zxpwin/cicd-test-docker'
                sh  'kubectl expose deployment cicd --port=8082 --type=NodePort'
  
            }
       }
   } 
}
```

注意这里使用的镜像是经过封装的，里面安装了docker和kubctl命令，以及maven环境。具体的Dockerfile如下：

```Docker
RUN yum update -y \
&& curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/linux/amd64/kubectl \
&& chmod +x kubectl \
&& mv kubectl /usr/local/bin/kubectl \
&& yum install maven -y \
&& yum install wget -y \
&& wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo \
&& yum -y install docker-ce-18.06.1.ce-3.el7
```

安装docker，然后将宿主机的docker挂载到我们的容器中，就可以了容器中构建镜像了。如果安装了kubectl命令，就可以直接部署项目到k8s集群中。实现docker的挂载在容器的模板中有实现。

![](https://raw.githubusercontent.com/zxpgo/images/master/img/20190811201049.png)