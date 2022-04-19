

[Kubernetes](https://github.com/kubernetes/kubernetes) 是 Google 基于 Borg 开源的容器编排调度引擎，作为 CNCF[1]（Cloud Native Computing Foundation）最重要的组件之一，它的目标不仅仅是一个编排系统，而是提供一个规范，可以让你来描述集群的架构，定义服务的最终状态，Kubernetes 可以帮你将系统自动地达到和维持在这个状态。Kubernetes 作为云原生应用的基石，相当于一个 **云操作系统** ，其重要性不言而喻。



Kubernetes 的重要性就如同 Linux 对操作系统的重要性一样，甚至 Kubernetes 的分发也和 Linux 发行版类似，Google 就像 Linux 内核开发团队，开发和维护着 Kubernetes 的核心，其他公司则通过 Certified Kubernetes 推出自己的 Kubernetes 发行版，因此出现几十甚至上百种 Kubernetes 发行版也不足为奇。

比较出名的两个发行版是：

- [Rancher](https://wiki.huihoo.com/wiki/Rancher)：全球唯一一个提供 Kubernetes、Mesos 和 Swarm 三种调度平台的企业级容器管理平台。
- [OpenShift](https://wiki.huihoo.com/wiki/OpenShift)：Red Hat 推出的 PaaS 开源云平台。



> [1]：CNCF：可能是目前最重要的云计算基金会，它的一大波开源软件在 GitHub 上占据着重要位置，如 [Kubernetes](https://github.com/kubernetes/kubernetes) 、[Prometheus](https://github.com/prometheus/prometheus)。



Kubernetes 是构建云原生的基石，了解 Cloud Native，看[宋净超博客](https://jimmysong.io/)。



## 为什么要用 K8S

K8s是 PaaS 平台的巨无霸。

![](./images/saas-paas-iaas-difference-1.png)

图片[引自](https://www.aalpha.net/blog/the-difference-between-paas-iaas-and-saas/)



## K8S 能做什么

作为容器调度和编排系统，K8s 能做：

![](./images/k8sdo.png)

图片[引自](https://kaiwu.lagou.com/course/courseInfo.htm?courseId=447#/detail/pc?id=4518)



## K8s 周边

- [KubeVirt](https://kubevirt.io/)：纳管虚拟机



## K8S 基本概念和术语

- **容器** ：K8S 主要管理的对象，其中又以 Docker 最为流行。目前 K8S 也支持管理虚拟机（通过 KubeVirt 插件）。

- **Cluster** ：集群，是计算、存储和网络资源的集合。K8S 基于这些资源构建各种容器化应用。

- **Master** ：Cluster 调度器，决定应用放在哪里运行。实际上是一台 Linux 主机，可以是物理机或是虚拟机，为了保证高可用，一个 Cluster 可以有多个 Master。

- **Node** ：Cluster 应用执行者，负责运行具体的容器应用。由 Master 管理，负责监控 Cluster 容器状态并上报。Node 也可以部署多个。

- **Pod** ：K8S 最小工作单位。每个 Pod 包含一个或多个容器，作为一个整体供 Master 进行调度。
- **Service** ：提供外界访问一组特定 Pod 的接口，可以为访问 Pod 实现负载均衡。
- **Controller** ：负责统一管理 Pod，它定义 Pod 的部署属性，比如有几个副本，在什么样的 Node 上运行，副本异常怎么处理等。为了满足多种业务场景，K8S 提供了多种 Controller，包括 Deployment、ReplicaSet、DaemonSet、StatefulSet、Job 等。

- **Deployment** ：Deployment 是最常用的 Controller，定义了用户的期望场景，实现了 Pod 的多副本管理，如果运行过程中有一个副本挂了，那么 K8S 会根据 Deployment 的定义重新拉起一个副本继续工作，始终保证 Pod 按照用户期望的状态运行。
- **ReplicaSet** ：ReplicaSet 和 Deployment 实现了同样的功能，确切的说是 Deployment 通过 ReplicaSet 来实现 Pod 的多副本管理。我们通常不需要直接使用 ReplicaSet。
- **DaemonSet** ：DaemonSet 用于每个 Node 最多只运行一个副本的场景，通常用于运行 Daemon。
- **Job** ：Job 用于运行结束就删除的应用，而其他 Controller 则是会长期保持运行。
- **StatefulSet** ：以上 Controller 都是无状态的，也就是说副本的状态信息会改变，比如当某个 Pod 副本异常重启时，其名称会改变。StatefulSet 提供有状态的服务，能够保证 Pod 的每个副本在其生命周期中名称保持不变。这是通过持久化的存储卷来实现的。
- **Namespace** ：负责 Cluster 资源的隔离。基于 Linux 的 Namespace 实现。K8S 默认会创建两个 Namespace：`default` 和 `kube-system` 。



## K8S 的安装与升级

安装方法总结：

- 本地玩耍：[minikube](https://github.com/kubernetes/minikube) or [kind](https://github.com/kubernetes-sigs/kind)：阿里云维护一个国内友好的 [minikube](https://github.com/AliyunContainerService/minikube)
- 社区独立部署工具：[kubeadm](https://github.com/kubernetes/kubeadm) ，搭建生产环境[高可用集群](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/high-availability/)
- 二进制安装：见下面的链接

- 运维工具自动化部署方案：SaltStack、Ansible（可部署生产可用的高可用集群）



其他工具：

- [kubeasz](https://github.com/easzlab/kubeasz) ：提供快速部署高可用 k8s 集群的工具，既有一键安装脚本，也可以根据指南分步安装各个组件
- [kubespray](https://github.com/kubernetes-incubator/kubespray) ：另一个可部署生产环境的项目
- [kubesh](https://github.com/SongCF/kubesh) ：一个二进制安装脚本，做到自动化安装
-  [kops](https://github.com/kubernetes/kops) ：可以帮助你在各大公有云上搭建 Kubernetes 集群，使用起来就像 kubectl 一样方便。
- 手动离线安装二进制教程：https://www.jianshu.com/p/8067912667f1



> 安装 docker 的时候，建议使用 Ubuntu 的 docker.io安装源，原因是 Docker 公司每次发布的最新的 Docker CE（社区版）产品往往还没有经过 Kubernetes 项目的验证，可能会有兼容性方面的问题。



阿里 k8s 源：http://mirrors.aliyun.com/kubernetes/



### minikube

和 Kind 相比，Minikube 的功能就强大的多了。虽说两者都是用于搭建本地集群的，但是 minikube 支持虚拟化的能力。minikube 可以借助于本地的虚拟化能力，通过 [Hyperkit](https://minikube.sigs.k8s.io/docs/drivers/hyperkit/)、[Hyper-V](https://minikube.sigs.k8s.io/docs/drivers/hyperv/)、[KVM](https://minikube.sigs.k8s.io/docs/drivers/kvm2/)、[Parallels](https://minikube.sigs.k8s.io/docs/drivers/parallels/)、[Podman](https://minikube.sigs.k8s.io/docs/drivers/podman/)、[VirtualBox](https://minikube.sigs.k8s.io/docs/drivers/virtualbox/) 和 [VMWare](https://minikube.sigs.k8s.io/docs/drivers/vmware/) 等创建出虚拟机，然后在虚拟机中搭建出 Kubernetes 集群来。这里可以根据自己的实际情况，选择合适的 [driver](https://minikube.sigs.k8s.io/docs/drivers/)。

当然，Minikube 也支持和 Kind 相似的能力，直接利用 Docker 创建集群：

```sh
$ minikube start --driver=docker \
  --imageRepository=registry.cn-hangzhou.aliyuncs.com/google_containers \
  --imageMirrorCountry=cn
```



### kubeadm

优势：

- 使用 Kubeadm 可以快速搭建出符合[一致性测试认证](https://www.cncf.io/certification/software-conformance/)（Conformance Test）的集群。
- Kubeadm 用户体验非常优秀，使用起来非常方便，并且可以用于搭建生产环境，支持[搭建高可用集群](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/high-availability/)。
- Kubeadm 的代码设计 **采用了可组合的模块方式** ，所以你可以只使用 Kubeadm 的部分功能，比如使用 Kubeadm 帮你生成各个组件的证书，也可以基于 kubeadm 开发专属的集群部署工具，比如通过 Ansible 借助于 Kubeadm 的子功能来定制 Kubernetes 集群的搭建。你可以通过 [Kubeadm init phase](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-init-phase/) 和 [Kubeadm join phase](https://kubernetes.io/zh/docs/reference/setup-tools/kubeadm/kubeadm-join-phase/)，去了解更多 Kubeadm 创建集群时内部各个子阶段的功能，并根据需要选择合适的子功能。
- 最为关键的是， **Kubeadm 可以向下兼容低一个小版本的 Kubernetes** ，也就意味着，你可以用 v1.18.x 的 kubeadm 搭建 v1.17.y 版本的 Kubernetes。
- 同时 **kubeadm 还支持集群平滑升级到高版本** ，即你可以使用 v1.17.x 版本的 Kubeadm 将 v1.16.y 版本的 Kubernetes 集群升级到 v1.17.z。同理，你可以继续使用高版本的 Kubeadm 将你的集群一点点升到想要的版本上去。



### 升级

详细见：https://kaiwu.lagou.com/course/courseInfo.htm?courseId=447#/detail/pc?id=4520



## K8S demo 体验

试用环境：https://www.katacoda.com/courses/kubernetes/playground

也有一些交互课程，熟悉K8s：https://www.katacoda.com/courses/kubernetes



## K8S 架构

K8S 从物理拓扑上可以分为 Master 节点和 Node 节点，符合主 Master 多 Slaver 的架构。如下图为部署图及相关的组件分布。

![](./images/k8sover.png)

![](images/k8sarch.jpg)

### Master

Master 是 K8S 集群的调度器，负责集群的资源管理和应用调度，充当首脑的角色，它实际上是一台 Linux 主机，可以是物理机或是虚拟机，为了保证高可用，一个集群可以有多个 Master。

Master 的运行以下组件：

- `kube-apiserver`：提供 K8S 集群的外部接口 API 的服务。K8S 对外提供的 HTTP/HTTPS RESTful API。各种客户端工具（CLI 或 GUI），以及 K8S 其他组件通过它访问和 管理 K8S 的资源。
- `kube-scheduler`：K8S 调度器服务。决定哪些 Pod 放在哪个 Pod 上运行。
- `kube-controller-manager`：K8S 集群容器资源编排和管理的服务。保证 K8S 的资源始终处于预期的状态。
- `etcd`：保存 K8S 集群各种资源信息的分布式数据库服务。一个分布式高可用的 key-value 存储服务，用于 K8S 服务发现和配置存储。

### Node

- `kubelet`：每个 Node 上负责容器管理的代理，依赖于 CRI（容器运行时接口），负责创建和运行容器，并向 Master 报告运行状态。
- `kube-proxy`：提供负载均衡的服务，为 service 服务，转发 service 上收到的请求到后端的 Pod。
- `container runtime`：K8s 支持多种容器运行时，并且不关心你部署的是什么运行时，都可以通过 CRI 接到 K8s 中。

> PS：Master 是一种特殊的 Node，也就是说，Master 上也可以运行 kubelet 和 kube-proxy 服务。



### Add-on

- `flannel` ：Pod 网络服务，Pod 之间、Pod 与外网通信的网络方案，也可以是 calico、canel 等方案。
- `CoreDNS`：为 K8s 集群提供 DNS 和服务发现服务。
- `Ingress Controller`：为服务提供外网接入的能力
- `Dashboard`：提供 GUI 可视化界面
- `Fluentd+Elasticsearch`：为集群提供日志采集、存储和查询等能力
- ...



### Master 和 Node 交互

- kubelet 主动上报状态，API server 不会主动跟 Kubelet 建立请求
- kubelet 会定时向 APIServer 汇报心跳，包括自身健康状态、负载数据统计等，如果一段时间没有更新，那么 Kube-Controller-Manager会将其标为 NodeLost 失联
- 当增加 Node 时，启动 kubelet 进程，它会主动向 APIServer 注册自己，APIServer 纳管 Node
- 各个组件以 APIserver 为中心，借助声明式API，各组件通过 Watch 机制，根据对象的变化进行相应的处理



### 组件间的通信方式

如上图所示，主要有 HTTP/HTTPS、gRPC、protobuf、DNS、系统调用等。



## K8s 组件

### kube-apiserver

### kube-controller manager

负责 K8s 控制器管理的组件，



### kube scheduler

### etcd



### kubelet

- kubelet 负责具体 Node 上的容器操作，通过 CRI 接入多种 container runtime，完成具体的容器的增删查改等；通过 CNI 接入多种容器网络插件为容器提供网络服务，通过 CSI 接入多种容器存储插件为容器提供存储服务
- 向 API server 上报 Node 的状态，上报频率默认是 10s



**注意：** kubelet 是作为一个守护进程直接运行在宿主机上的，并不像 K8s 其他组件作为容器运行在宿主机上，原因是如上面所说，kubelet 除了跟容器运行时打交道外，在配置容器网络、管理容器数据卷时， **都需要直接操作宿主机。如果部署在容器里，由于容器的隔离性，直接操作宿主机会非常麻烦。**

所以，一般在部署 K8s 集群之前，都会手动安装 kubelet 在内的三个组件：

```sh
# ubuntu
apt-get install -y kubeadm kubelet kubectl
# centos
yum install -y kubeadm kubelet kubectl
```



### kube-proxy

负责 Kubernetes service 的负载均衡，它会监听 Kubernetes 中的 service 和 endpoint，当 service 或 endpoint 发生变化时，就会调用相应的接口创建出对应的规则。

![](./images/kube-proxy.png)



三种工作模式：

- userspace

- iptables：简单，但性能低
- IPVS：支持更高吞吐和复杂的负载均衡策略，[参考](https://kubernetes.io/zh/docs/concepts/services-networking/service/#proxy-mode-ipvs)

目前社区也有基于 eBPF 实现的，比如腾讯实现的 [IPVS-BPF](https://mp.weixin.qq.com/s?__biz=MzI5ODQ2MzI3NQ==&mid=2247491111&idx=2&sn=db348d6f13e1df4b3b9aba2dce0ba0e4&chksm=eca42763dbd3ae757530f6922ca1748736e42eb863e01076e94622c81be542e5582c9678874b&scene=27#wechat_redirect)



### kubeadm

Kubernetes 社区维护的一个部署 K8s 集群的工具，一般用条指令就可以部署一个 K8s 集群：

```sh
# 创建一个Master节点
$ kubeadm init

# 将一个Node节点加入到当前集群中
$ kubeadm join <Master节点的IP和端口>
```

但显然这样一键部署出来的集群，无法用于生产环境！

原因是：kubeadm 无法部署高可用的集群，什么是高可用？比如像 Etcd、Master 组件都应该是多节点集群，而不是现在这样的单点。

所以，结论是： **kubeadm 不适用于大规模集群，仅用于快速搭建测试集群。** 但不代表以后不会支持！

【2020.10补充】：kubeadm 已经支持生产环境部署：

[Production-Ready Kubernetes Cluster Creation with kubeadm](https://kubernetes.io/blog/2018/12/04/production-ready-kubernetes-cluster-creation-with-kubeadm/)

[Creating Highly Available clusters with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/)



### kubectl



## Pod

- 就像线程是 Linux 任务调度的基本单元，Pod 也是 K8s 资源调度的基本单元
- Pod 里的所有容器，共享的是同一个 Network Namespace，并且可以声明共享同一个 Volume
- 基本上 K8s 所有的资源操作都是围绕着 Pod 来转的。如下：

![](./images/podcore.png)





什么样的容器适合放在一个 Pod 中，一般来说，有以下场景：

一般来说，在一个 Pod 内运行多个容器，比较适应于以下这些场景。

- 容器之间会发生文件交换等，上面提到的例子就是这样。一个写文件，一个读文件。

- 容器之间需要本地通信，比如通过 localhost 或者本地的 Socket。这种方式有时候可以简化业务的逻辑，因为此时业务就不用关心另外一个服务的地址，直接本地访问就可以了。

- 容器之间需要发生频繁的 RPC 调用，出于性能的考量，将它们放在一个 Pod 内。

- 希望为应用添加其他功能，比如日志收集、监控数据采集、配置中心、路由及熔断等功能。这时候可以考虑利用边车模式（Sidecar Pattern），既不需要改动原始服务本身的逻辑，还能增加一系列的功能。比如 Fluentd 就是利用边车模式注入一个对应 log agent 到 Pod 内，用于日志的收集和转发。 Istio 也是通过在 Pod 内放置一个 Sidecar 容器，来进行无侵入的服务治理。



### 应用

从应用的角度来说，Pod 就是 Kubernetes 世界里的“应用”；而一个应用，可以由多个容器组成。

K8s 创建应用有两种方式：

- kubectl 命令直接创建
- 通过 yaml 配置文件



**1. kubectl 命令**

和 `docker run` 类似：

```
kubectl run nginx --image=nginx:1.7.9 --replicas=2
```

通过 `kubectl run` 命令，通过 `--image`，`--replicas` 等参数指定资源的属性。

（如果本地有镜像，直接使用，如果没有，则会到远程仓库获取镜像，自动部署）



**2. yaml 配置文件**

需要结合 `kubectl apply` 命令（该命令除了可以创建资源外，还可以更新资源）：

```
kubectl apply -f nginx.yml
```



### Infra 容器

既然共享，就存在谁共享谁，谁先启动的问题（鸡生蛋，蛋生鸡）。这样 **Pod 里的容器就不是对等关系，而是拓扑关系了。**

所以，在 K8s 里，Pod 的实现需要使用一个中间容器，这个容器叫作 **Infra 容器** 。

在这个 Pod 中，Infra 容器永远都是第一个被创建的容器，而其他用户定义的容器，则通过 Join Network Namespace 的方式，与 Infra 容器关联在一起。如下图：

![](./images/infra.png)

Infra 容器占用非常少的资源，它使用一个非常特殊的镜像，即 `k8s.gcr.io/pause`，这个镜像是一个用汇编语言编写的、永远处于“暂停”状态的容器，解压后的大小也只有 100~200 KB 左右。

有了 Infra 容器，一个 Pod 就可以做到：

- 它们可以直接使用 localhost 进行通信；
- 它们看到的网络设备跟 Infra 容器看到的完全一样；
- 一个 Pod 只有一个 IP 地址，也就是这个 Pod 的 Network Namespace 对应的 IP 地址；（有一个插件叫 [multus](https://github.com/intel/multus-cni) 可以做到一个 Pod 多个 IP）
- 当然，其他的所有网络资源，都是一个 Pod 一份，并且被该 Pod 中的所有容器共享；
- Volume 也是一个 Pod 所有容器共享
- Pod 的生命周期只跟 Infra 容器一致，而与容器 A 和 B 无关。
- 设计容器网络插件，要以 Infra 容器的 ns 为主，而不应该关心容器



### Init Container

优先启动的容器，或者具有顺序关系的容器。通常用来做一些初始化工作，比如环境检测、OSS 文件下载、工具安装，等等。

在 Pod 中，所有 Init Container 定义的容器，即 sepc.initContainer，都会比 spec.containers 定义的用户容器先启动。并且，Init Container 容器会严格按顺序逐一启动，而直到它们都启动并且退出了，用户容器才会启动。

应用容器专注于业务处理，其他一些无关的初始化任务就可以放到 init 容器中。这种解耦有利于各自升级，也降低相互依赖。

一个 Pod 中允许有一个或多个 init 容器。init 容器和其他一般的容器非常像，其与众不同的特点主要有：

- 总是运行到完成，可以理解为一次性的任务，不可以运行常驻型任务，因为会 block 应用容器的启动运行；
- 顺序启动执行，下一个的 init 容器都必须在上一个运行成功后才可以启动；
- 禁止使用 readiness/liveness 探针，可以使用 Pod 定义的 **activeDeadlineSeconds** ，这其中包含了 Init Container 的启动时间；
- 禁止使用 lifecycle hook。



这种组合方式，是容器设计模式里最常用的一种模式，它有一个名字叫：sidecar。



**例子1：War 包与 Web 服务器**

一个 Java 的 war 包，需要放到 Tomcat 的  webapps 目录下运行。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: javaweb-2
spec:
  initContainers:
  - image: geektime/sample:v2
    name: war
    command: ["cp", "/sample.war", "/app"]
    volumeMounts:
    - mountPath: /app
      name: app-volume
  containers:
  - image: geektime/tomcat:7.0
    name: tomcat
    command: ["sh","-c","/root/apache-tomcat-7.0.42-v2/bin/start.sh"]
    volumeMounts:
    - mountPath: /root/apache-tomcat-7.0.42-v2/webapps
      name: app-volume
    ports:
    - containerPort: 8080
      hostPort: 8001 
  volumes:
  - name: app-volume
    emptyDir: {}
```

存放 war 包的容器声明为 Init Container，并将 war 包拷贝到共享 Volume 中，Web 应用容器就可以从共享 Volume 中取到这个 war 包进行运行。



**例子2：容器日志的收集**

在 Pod 里有一个应用容器，需要不断把日志输出到 /var/log 目录中，就可以用一个共享 Volume 挂载到 /var/log ，另外，运行一个 sidecar 容器，同时也声明挂载 Volume 到 /var/log，然后就可以不断从 /var/log 中读取日志文件，转发到 MongoDB 或 Elasticsearch 中存储起来，这就是一个基本的日志收集工作。



### sidecar 容器

上面说的 Init Container 就是一种 sidecar 容器。sidecar 更通用的使用是作为一种辅助容器，为主要的容器服务。

最典型的就是微服务治理的项目 istio，就是基于 sidecar 的思想设计实现的。



### static Pod

这是一种特殊的容器启动方法，如在用 kubeadm 部署集群的过程中，kube-apiserver、controller manager、scheduler 等 Pod 就是 static Pod 来部署的。

它允许你把要部署的 Pod 的 YAML 文件放在一个指定的目录里。这样，当这台机器上的 kubelet 启动时，它会自动检查这个目录，加载所有的 Pod YAML 文件，然后在这台机器上启动它们。

在 kubeadm 中，Master 组件的 YAML 文件会被生成在 `/etc/kubernetes/manifests` 目录下。

```sh
# ls /etc/kubernetes/manifests/
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```

它们都是以 static Pod 的方式来启动的。



### Pod 的生命周期(运行状态)

由 Pod API 对象的 Status 字段定义。其中的 `pod.status.phase` 字段就是 Pod 的当前状态。有以下几种情况：

- **Pending：** 1）还未被调度(调度不成功)；2）Pod 内的容器镜像不存在，需从镜像中心拉取
- **Running：** Pod 已经调度成功，跟一个具体的节点绑定。它包含的容器都已经创建成功，并且 *至少有一个正在运行中。*
- **Succeeded：** Pod 里的所有容器都正常运行完毕，并且已经退出了，退出码为0。这种情况 *在运行一次性任务时最为常见。*
- **Failed：** Pod 里 *至少有一个容器* 以不正常的状态（非 0 的返回码）退出。一般都是容器运行异常退出，或者被系统终止，意味着你得想办法 Debug 这个容器的应用，比如查看 Pod 的 Events 和日志。
- **Unknown：** Pod 的状态不能持续地被 kubelet 汇报给 kube-apiserver，这很有可能是 *主从节点（Master 和 Kubelet）间的通信* 出现了问题，Node 失联



除此之外，还有一些细分的状态，由 Conditions 字段定义。这些状态主要用来描述出现当前的 Status 的原因是什么？

- **PodScheduled：** 
- **Ready：** 注意和 Running 的区别，Running 是正常启动，而 Ready 不仅是正常启动，而且是可以对外提供服务了
- **Initialized：**
- **Unschedulable：** 若当前状态是 Pending ，对应 Condition 是 Unschedulable，意味着调度出现问题

> 对于 Pod 状态是 Ready，实际上不能提供服务的情况能想到几个例子：
>
> 1. 程序本身有 bug，本来应该返回 200，但因为代码问题，返回的是500；
> 2. 程序因为内存问题，已经僵死，但进程还在，但无响应；
> 3. Dockerfile 写的不规范，应用程序不是主进程，那么主进程出了什么问题都无法发现；
> 4. 程序出现死循环。





### 容器的恢复机制(重启策略)

由 Pod 的 Spec 部分的 `pod.spec.restartPolicy` 字段来定义。它有三个值：

- Always：任何时候容器发送了异常，即任何情况下只要容器不在运行状态，它一定会被重新创建，Deployment 的 restartPolicy 永远是 Always
- OnFailure：只在容器异常退出，且退出码不为0时，才自动重启容器
- Never：从来不重启容器

关于这几种状态，以及 Pod 状态的对应关系，[官网总结了一大堆情况](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#example-states)。但其实只用记住下面两条：

1. 只要 Pod 的 restartPolicy 指定的策略允许重启异常的容器（比如：Always），那么这个 Pod 就会保持 Running 状态，并进行容器重启。否则，Pod 就会进入 Failed 状态 。

2. 对于包含多个容器的 Pod，只有它里面所有的容器都进入异常状态后，Pod 才会进入 Failed 状态。在此之前，Pod 都是 Running 状态。此时，Pod 的 READY 字段会显示正常容器的个数，比如：

    ```sh
    $ kubectl get pod test-liveness-exec
    NAME           READY     STATUS    RESTARTS   AGE
    liveness-exec   0/1       Running   1          1m
    ```

**值得注意的是：** Pod 的恢复过程，永远都是发生在当前节点上，而不会跑到别的节点上去。事实上，一旦一个 Pod 与一个节点（Node）绑定，除非这个绑定发生了变化（pod.spec.node 字段被修改），否则它永远都不会离开这个节点。这也就意味着，如果这个宿主机宕机了，这个 Pod 也不会主动迁移到其他节点上去。而如果你想让 Pod 出现在其他的可用节点上，就必须使用 Deployment 这样的“控制器”来管理 Pod，哪怕你只需要一个 Pod 副本。Deployment 会决策应该迁移到什么节点上。



### 容器的健康检查

K8s 使用健康检查的探针（Probe）来判断容器的健康存活情况，这种机制，是生产环境中保证应用健康存活的重要手段。

其中有两个重要的字段：

- livenessProbe：探活，探测容器是否真的在运行，如果探测失败，kubelet 就会停掉该容器，后续的操作就又受到重启策略的影响
- readinessProbe：指示容器是否可以正常对外提供服务，比如 nginx 容器在 reload 配置的时候就无法对外提供 HTTP 服务
- startupProbe：判断容器是否已经启动好，比如容器启动慢，可以通过该参数，保证有足够长的时间来应付“超长”的启动时间。1.18 版本加入。



每种 Probe 都可以用三种方法 Handler 来进行探针：

- exec：执行一个命令，返回错误即表示容器内部出现异常
- HTTP：发起 HTTP 请求
- TCP：发起 TCP 请求

> 注：对于每一种 Probe，Kubelet 只会执行其中一种 Handler。如果你定义了多个 Handler，则会按照 Exec、HTTPGet、TCPSocket 的优先级顺序，选择第一个定义的 Handler。



三种对应 yaml 格式如下：

```yaml
...
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

```yaml
...
livenessProbe:
     httpGet:
       path: /healthz
       port: 8080
       httpHeaders:
       - name: X-Custom-Header
         value: Awesome
       initialDelaySeconds: 3
       periodSeconds: 3
```

```yaml
	...
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```

总结来看：Pod 其实可以暴露一个健康检查 URL（比如 /healthz），或者直接让健康检查去检测应用的监听端口。这两种配置方法，在 Web 服务类的应用中非常常用。



一个综合的例子，定义了三种 Probe：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: probe-demo
  namespace: demo
spec:
  containers:
  - name: sise
    image: quay.io/openshiftlabs/simpleservice:0.5.0
    ports:
    - containerPort: 9876
    readinessProbe:
      tcpSocket:
        port: 9876
      periodSeconds: 10
    livenessProbe:
      periodSeconds: 5
      httpGet:
        path: /health
        port: 9876
    startupProbe:
      httpGet:
        path: /health
        port: 9876
      failureThreshold: 3
      periodSeconds: 2
```

优先通过 startupProbe 判断服务是不是启动好，之后再每隔 5s 进行一次探活检测和 每隔 10s 进行一次就绪检测。



## yaml文件

Pod 是由容器组成，而容器是通过容器镜像构建起来，因此，在 K8s 中部署一个应用时，只需要部署该应用所对应的镜像即可。

K8s 中可以使用 kubectl 命令来部署应用（和 docker 命令类似），但有更优雅的方式，就是通过 yaml 文件。它可以说是 K8s 资源的说明书，可以做很多命令行做不到的工作。

K8S 所有的资源都可以写成 yaml 文件的格式，yaml 文件是 K8S 特有的配置文件，可以把容器的定义、参数、配置，统统记录在一个 YAML 文件中。类似于 Python 的 `.ini` 文件，Java 的 `.properties` 文件。yaml 文件的语法格式类似于 JSON：

例子：K8s 通过 yaml 文件来启动一个容器化应用或任务。

![](images/yaml.jpg)

- ① `apiVersion` 是当前配置格式的版本。
- ② `kind` 是要创建的资源类型，这里是 `Deployment`。
- ③ `metadata` 是该资源的元数据，`name` 是必需的元数据项。
- ④ `spec` 部分是该 `Deployment` 的规格说明。
- ⑤ `replicas` 指明副本数量，默认为 1。
- ⑥ `template` 定义 Pod 的模板，这是配置文件的重要部分。
- ⑦ `metadata` 定义 Pod 的元数据，至少要定义一个 label。label 的 key 和value 可以任意指定。
- ⑧ `spec` 描述 Pod 的规格，此部分定义 Pod 中每一个容器的属性，`name` 和 `image` 是必需的。

> 一个 Kubernetes 的 API 对象的定义，大多可以分为 Metadata 和 Spec 两个部分。前者存放的是这个对象的元数据，对所有 API 对象来说，这一部分的字段和格式基本上是一样的；而后者存放的，则是属于这个对象独有的定义，用来描述它所要表达的功能。

一个比较完整的 Nginx 应用的 yaml 文件如下：

```yaml
apiVersion: v1 #指定当前描述文件遵循v1版本的Kubernetes API
kind: Pod #我们在描述一个pod
metadata:
  name: twocontainers #指定pod的名称
  namespace: default #指定当前描述的pod所在的命名空间
  labels: #指定pod标签
    app: twocontainers
  annotations: #指定pod注释
    version: v0.5.0
    releasedBy: david
    purpose: demo
spec:
  containers:
  - name: sise #容器的名称
    image: quay.io/openshiftlabs/simpleservice:0.5.0 #创建容器所使用的镜像
    ports:
    - containerPort: 9876 #应用监听的端口
  - name: shell #容器的名称
    image: centos:7 #创建容器所使用的镜像
    command: #容器启动命令
    - "bin/bash"
    args:
    - "-c"
    - "sleep 10000"
```

执行 `kubectl appy -f nginx-deployment.yaml` 就可以部署具有两个副本的 Nginx 应用。

完了进入 shell 容器访问 sise 服务：

```sh
$ kubectl exec twocontainers -c shell -i -t -- bash
[root@twocontainers /]# curl -s localhost:9876/info
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

### Pod yaml 文件的核心字段

#### API 模板

metadata + spec + status

**metadata**

包含 namespace + name + uid + labels + annotations

namespace：对一组资源和对象的抽象集合

- default：缺省的命名空间
- kube-system：部署集群的关键的核心组件
- kube-public：由 kubeadm 创建，保存一些集群 bootstrap 的信息，比如 token 等
- kube-node-lease：用于 node 汇报心跳



**spec**

- 描述了对象的详细配置信息，即用户希望的状态



**status**

- 包含该对象的一些状态信息
- 是各组件之间进行信息同步的标志



#### NodeSelector

是一个供用户将 Pod 与 Node 进行绑定的字段

```yaml
apiVersion: v1
kind: Pod
...
spec:
 nodeSelector:
   disktype: ssd
```

表明这个 Pod 永远只能运行在携带了“disktype: ssd”标签（Label）的节点上；否则，它将调度失败。

#### NodeName

一旦 Pod 的这个字段被赋值，Kubernetes 项目就会被认为这个 Pod 已经经过了调度，调度的结果就是赋值的节点名字。

#### HostAliases

定义了 Pod 的 hosts 文件（比如 /etc/hosts）里的内容：

```yaml
apiVersion: v1
kind: Pod
...
spec:
  hostAliases:
  - ip: "10.1.2.3"
    hostnames:
    - "foo.remote"
    - "bar.remote"
...
```

Pod 启动后，对应 /etc/hosts 为：

```sh
cat /etc/hosts
# Kubernetes-managed hosts file.
127.0.0.1 localhost
...
10.244.135.10 hostaliases-pod
10.1.2.3 foo.remote
10.1.2.3 bar.remote
```

> 注意：在 Kubernetes 项目中，如果要设置 hosts 文件里的内容，一定要通过这种方法。否则，如果直接修改了 hosts 文件的话，在 Pod 被删除重建之后，kubelet 会自动覆盖掉被修改的内容。

#### share namespace

包括与 Pod 中的其他容器共享 namespace 和与 host 共享 namespace。

**① shareProcessNames：**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  shareProcessNamespace: true
  containers:
  - name: nginx
    image: nginx
  - name: shell
    image: busybox
    stdin: true
    tty: true
```

`shareProcessNamespace=true` ：意味着这个 Pod 里的容器要共享 PID Namespace。

```sh
$ kubectl apply -f nginx.yaml

$ kubectl attach -it nginx -c shell

$ kubectl attach -it nginx -c shell
/ # ps ax
PID   USER     TIME  COMMAND
    1 root      0:00 /pause
    8 root      0:00 nginx: master process nginx -g daemon off;
   14 101       0:00 nginx: worker process
   15 root      0:00 sh
   21 root      0:00 ps ax
```

可以看到，在这个容器里，我们不仅可以看到它本身的 ps ax 指令，还可以看到 nginx 容器的进程，以及 Infra 容器的 /pause 进程。这就意味着，整个 Pod 里的每个容器的进程，对于所有容器来说都是可见的：它们共享了同一个 PID Namespace。



**② hostNetwork、hostIPC、hostPID：**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  hostNetwork: true
  hostIPC: true
  hostPID: true
  containers:
  - name: nginx
    image: nginx
  - name: shell
    image: busybox
    stdin: true
    tty: true
```

义了共享宿主机的 Network、IPC 和 PID Namespace。这就意味着，这个 Pod 里的所有容器，会直接使用宿主机的网络、直接与宿主机进行 IPC 通信、看到宿主机里正在运行的所有进程。

#### Container

和 Docker 定义字段一样，如有：Image（镜像）、Command（启动命令）、workingDir（容器的工作目录）、Ports（容器要开发的端口），以及 volumeMounts（容器要挂载的 Volume）都是构成 Kubernetes 项目中 Container 的主要字段。

除此之外，还有：

##### ImagePullPolicy

定义了镜像拉取的策略，有三个值：

- Always：默认，每次创建 Pod 都会拉取一次镜像
- Never 或者 IfNotPresent：只有宿主机不存在这个镜像才会拉取

##### Lifecycle：PostStart + PreStop

定义了容器状态发生变化时触发的事件。典型的就是在容器启动之后打印一条欢迎消息，容器删除之前，先执行应用退出的命令，达到优雅退出的目的。

k8s 里定义两种 hook：

- PostStart 可以在容器启动之后就执行。但需要注意的是，此 hook 和容器里的 ENTRYPOINT 命令的执行顺序是不确定的。
- PreStop 则在容器被终止之前被执行，是一种阻塞式的方式。执行完成后，Kubelet 才真正开始销毁容器。



两种 hook 有两种 Handler：

- Exec 用来执行 Shell 命令，看下面的例子
- HTTPGet 可以执行 HTTP 请求。



如下 yaml 文件中 preStart 和 preStop 字段就是完成这个事：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: lifecycle-demo
spec:
  containers:
  - name: lifecycle-demo-container
    image: nginx
    lifecycle:
      postStart:
        exec:
          command: ["/bin/sh", "-c", "echo Hello from the postStart handler > /usr/share/message"]
      preStop:
        exec:
          command: ["/usr/sbin/nginx","-s","quit"]
```

### PodPreset - 自动编写 yaml 文件

Pod 的字段很多，我们不可能完全记住，K8s 提供了某种方法能够自动给 Pod 填充某些字段，这个方法就是 PodPreset（Pod 预设置），在 v.1.11 之后的版本中出现。

例子：

开发人员编写一个基本的 pod.yaml 模板：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: website
  labels:
    app: website
    role: frontend
spec:
  containers:
    - name: website
      image: nginx
      ports:
        - containerPort: 80
```

运维人员编写一个 PodPreset 对象：preset.yaml

```yaml
apiVersion: settings.k8s.io/v1alpha1
kind: PodPreset
metadata:
  name: allow-database
spec:
  selector:
    matchLabels:
      role: frontend
  env:
    - name: DB_PORT
      value: "6379"
  volumeMounts:
    - mountPath: /cache
      name: cache-volume
  volumes:
    - name: cache-volume
      emptyDir: {}
```

然后先创建 preset，再创建 pod，便会自动生成一个完整的 Pod API 对象：

```sh
$ kubectl create -f preset.yaml
$ kubectl create -f pod.yaml
```

```yaml
$ kubectl get pod website -o yaml
apiVersion: v1
kind: Pod
metadata:
  name: website
  labels:
    app: website
    role: frontend
  annotations:
    podpreset.admission.kubernetes.io/podpreset-allow-database: "resource version"
spec:
  containers:
    - name: website
      image: nginx
      volumeMounts:
        - mountPath: /cache
          name: cache-volume
      ports:
        - containerPort: 80
      env:
        - name: DB_PORT
          value: "6379"
  volumes:
    - name: cache-volume
      emptyDir: {}
```

## Service

Service 提出的原因是因为 Pod 的 IP 地址会变（比如由于故障导致 Pod 销毁后又重建），从而导致应用的访问受到影响。

还有一个原因是 Pod 有负载均衡的需求。

Service 从逻辑上代表了一组 Pod，有固定的 IP ，作为 Pod 的代理入口（Portal），应用只需访问 Service 的 IP，Kubernetes 会负责为 Service 和后端具体的 Pod 之间建立连接。无论后端的 Pod 如何变化，应用的访问都不会受到影响。

![](./images/service.jpg)



Service 的存在还提供了一组 Pod 的负载均衡能力，使集群资源的管理更加简便和高效。

Service 的 yaml 文件格式：

![](images/serviceyaml.jpg)

① `v1` 是 Service 的 `apiVersion`。

② 指明当前资源的类型为 `Service`。

③ Service 的名字为 `httpd-svc`。

④ `selector` 指明挑选那些 label 为 `run: httpd` 的 Pod 作为 Service 的后端。

⑤ 将 Service 的 8080 端口映射到 Pod 的 80 端口，使用 TCP 协议。

### 集群内访问 Service 的两种方式

- **VIP：** 即 ClusterIP，设定一个 Service 的 IP，即 VIP，访问 VIP，Service 会将请求转发到它所代理的某个 Pod 上

- **DNS：** 访问 DNS 记录，DNS 记录域名与 IP 的关系，Service 也可以将请求转发到域名对应的代理 IP 上
	- **Normal Service：** 代理 IP 即为 Service 的 VIP，后面的过程就和 VIP 方式一致了
	- **Headless Service：** 代理 IP 为某个被代理 Pod 的 IP，不需要分配 VIP

### Headless Service

如下：Headless Service，其实仍是一个标准 Service 的 YAML 文件。只不过，它的 clusterIP 字段的值是：None，即：这个 Service，没有一个 VIP 作为“头”。这也就是 Headless 的含义。所以，这个 Service 被创建后并不会被分配一个 VIP，而是会以 DNS 记录的方式暴露出它所代理的 Pod（由 Label Selector 来标识，如：app=nginx）。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
```

当一个 Headless Service 被创建之后，它被代理的 Pod 就会绑定以下格式的 DNS 记录：

```sh
<pod-name>.<svc-name>.<namespace>.svc.cluster.local
```

**Headless Service 是用来实现 StatefulSet 的核心功能之一。**

### 从集群外访问 Service 的方法

集群外，包括其他的 K8s 集群，或者 Internet。

有三种方法：

- NodePort
- LoadBalancer
- externalName：1.7 之后的版本支持
- *externalIPs：严格来说不算*

#### NodePort

![](./images/nodeport.png)



```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  type: NodePort
  ports:
  - nodePort: 8080
    targetPort: 80
    protocol: TCP
    name: http
  - nodePort: 443
    protocol: TCP
    name: https
  selector:
    run: my-nginx
```

#### LoadBalancer

适用于公有云上的 K8s 服务

```yaml
---
kind: Service
apiVersion: v1
metadata:
  name: example-service
spec:
  ports:
  - port: 8765
    targetPort: 9376
  selector:
    app: example
  type: LoadBalancer
```

在公有云提供的 Kubernetes 服务里，都使用了一个叫作 CloudProvider 的转接层，来跟公有云本身的 API 进行对接。所以，在上述 LoadBalancer 类型的 Service 被提交后，Kubernetes 就会调用 CloudProvider 在公有云上为你创建一个负载均衡服务，并且把被代理的 Pod 的 IP 地址配置给负载均衡服务做后端。也就是需要跟各家云厂商做适配，详细看：https://kubernetes.io/zh/docs/concepts/services-networking/service/#loadbalancer



#### externalName

ExternalName 类型的 Service 在实际中使用的频率不是特别高，但是对于某些特殊场景还是有一些用途的。比如在云上或者内部已经运行着一个应用服务，但是暂时没有运行在 Kubernetes 中，如果想让在 Kubernetes 集群中的 Pod 访问该服务，这时当然可以直接使用它的域名地址，也可以通过 ExternalName 类型的 Service 来解决。这样就可以直接访问 Kubernetes 内部的 Service 了。

详细参考：https://kubernetes.io/zh/docs/concepts/services-networking/service/#externalname

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  type: ExternalName
  externalName: my.database.example.com
```

当你通过 Service 的 DNS 名字访问它的时候，比如访问：my-service.default.svc.cluster.local。那么，Kubernetes 为你返回的就是my.database.example.com。所以说，ExternalName 类型的 Service，其实是在 kube-dns 里为你添加了一条 **CNAME** 记录。这时，访问 my-service.default.svc.cluster.local 就和访问 my.database.example.com 这个域名是一个效果了。

#### externalIPs

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 9376
  externalIPs:
  - 80.11.12.10
```

上述 Service 中，我为它指定的 externalIPs=80.11.12.10，那么此时，你就可以通过访问 80.11.12.10:80 访问到被代理的 Pod 了。不过，在这里 Kubernetes 要求 externalIPs 必须是至少能够路由到一个 Kubernetes 的节点。

## K8S 控制器

K8s 定义了一系列的控制器，每一种控制器都有独有的编排功能。如下查看：

```sh
$ cd kubernetes/pkg/controller/
$ ls -d */              
deployment/             job/                    podautoscaler/          
cloud/                  disruption/             namespace/              
replicaset/             serviceaccount/         volume/
cronjob/                garbagecollector/       nodelifecycle/          replication/            statefulset/            daemon/
...
```



常用的有：

- Deployment 和 ReplicaSet 
- StatefulSet
- DaemonSet
- Job 和 Cronjob



### 控制循环

在有些地方看到 Reconcile、Reconcile Loop、Sync Loop 等词，都是表示 控制循环的意思。它是 K8s 进行容器资源编排的核心算法：即保证集群资源当前状态永远等于预期状态，如下伪代码所示：

```go
for {
  实际状态 := 获取集群中对象X的实际状态（Actual State）
  期望状态 := 获取集群中对象X的期望状态（Desired State）
  if 实际状态 == 期望状态{
    什么都不做
  } else {
    执行编排动作，将实际状态调整为期望状态
  }
}
```

它的 yaml 文件遵循以下模板：

![](./images/deploymodel.png)

包括上半部的控制器定义（包括期望状态）+下半部的被控制对象模板。

### Deployment 和 ReplicaSet

Deployment 管理应用的多个副本，使之维持在期待的状态。

实际上是通过 ReplicaSet 来创建资源，如下图所示：

![](images/deployarch.jpg)

1. 用户通过 kubectl 创建 Deployment
2. Deployment 创建 ReplicaSet
3. ReplicaSet 创建 Pod

从名称上来看， **对象的命名方式为：子对象的名字 = 父对象名字 + 随机字符串或数字。**

Deployment 简单的 Yaml 文件：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

ReplicaSet 的简单 yaml 文件：

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-set
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
```

一个 ReplicaSet 对象，其实就是由副本数目的定义和一个 Pod 模板组成的。不难发现，它的定义其实是 **Deployment 的一个子集。**

Deployment 和 ReplicaSet 结合可以实现 K8s 集群中应用的高可用、高扩展、高容错的管理，详细见【应用管理】小节。

#### 为什么需要 ReplicaSet

主要是为了实现应用升级（滚动更新）的无感知性。ReplicaSet 本质上负责的是应用的版本，即 Deployment 控制的是 ReplicaSet（版本），ReplicaSet 控制 Pod （副本数）。

滚动更新是 Deployment 和 ReplicaSet 相互作用实现的。

对一个应用来说，版本对应的正是一个 ReplicaSet，而这个版本应用的 Pod 数量，则由 ReplicaSet 控制器来保证。

**之所以有这两层关系，** 是因为在滚动更新的过程中，需要继续为用户提供服务，这就有一个从旧版本逐步切换到新版本的过程，也就是需要逐步删除旧版本的 Pod 数量，然后逐步增加新版本的 Pod 数量。这其中就包含两层关系：版本和具体的应用副本。所以，需要 Deployment 和 ReplicaSet 的两层关系来控制这个滚动更新的过程，Deployment 负责版本的控制，ReplicaSet 负责每个版本的副本控制。如下图为两层关系：

![](./images/replicaset.jpg)

![](./images/deprs.png)



### StatefulSet

Deployment 是认为所有 Pod 都是一样的，它们互相之间没有顺序，也无所谓运行在哪台宿主机上。

对于实际的场景中，很多应用，尤其是分布式应用，它们多个实例之间，往往有各种依赖关系，比如：主从关系、主备关系。

这个时候用 Deployment 就无能为力，StatefulSet 正好满足这样的应用。称为有状态的应用：即应用实例之间有不对等关系，以及实例对外部数据有依赖关系。

它主要描述两种有状态应用直接的关系：

- 拓扑状态：多个实例之间不对等，A 依赖 B，B 依赖 C，形成拓扑关系
- 存储状态：保证实例每次读取的数据都是同一份

#### StatefulSet 核心功能

StatefulSet 的实现需要保证：通过某种方式记录这些状态，然后在 Pod 被重建时，能够为 Pod 恢复这些状态。

这种方式就是 Headless Service。

Headless Service 的作用是没有 Service VIP，而是用 DNS 的方式请求，并通过 DNS 解析出被代理 Pod 的 IP 地址。

它的重点是：

- 以 DNS 的方式请求 Service
- DNS 解析出一个 Pod IP 进行访问

StatefulSet 可以利用这层信息，来通过 DNS 记录 Pod 的 IP，这样不管 Pod 被重建还是状态改变，都可以通过 DNS 访问到之前的 Pod。这里解决了 **状态访问和恢复** 的问题。

#### 拓扑状态

还有一个问题是状态间的联系，即 A 依赖 B，B 依赖 C。对于这个问题，StatefulSet 是通过 **名字+编号** 的方式来解决的。

举例：

一个标准的 StatefulSet 的 yaml 文件如下：

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.9.1
        ports:
        - containerPort: 80
          name: web
```

这个 YAML 文件，和 nginx-deployment 的唯一区别，就是多了一个 serviceName=nginx 字段。

创建 Headless service 和 StatefulSet：

```sh
$ kubectl create -f svc.yaml
$ kubectl get service nginx
NAME      TYPE         CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
nginx     ClusterIP    None         <none>        80/TCP    10s

$ kubectl create -f statefulset.yaml
$ kubectl get statefulset web
NAME      DESIRED   CURRENT   AGE
web       2         1         19s
```

查看创建过程：

```sh
# -w 监视创建的过程
$ kubectl get pods -w -l app=nginx
NAME      READY     STATUS    RESTARTS   AGE
web-0     0/1       Pending   0          0s
web-0     0/1       Pending   0         0s
web-0     0/1       ContainerCreating   0         0s
web-0     1/1       Running   0         19s
web-1     0/1       Pending   0         0s
web-1     0/1       Pending   0         0s
web-1     0/1       ContainerCreating   0         0s
web-1     1/1       Running   0         20s
```

可以看到，Pod 的实例是按名字+编号的方式创建的，并且具有顺序，在编号前的实例没 Running 起来之前，编号靠后的实例不能创建，处于 Pending。通过这种方式就保证了实例之间的顺序性。

Pod 的 hostname 与 Pod 名字一样：

```sh
$ kubectl exec web-0 -- sh -c 'hostname'
web-0
$ kubectl exec web-1 -- sh -c 'hostname'
web-1
```

以 DNS 的方式，通过访问 Headless Service，访问具体的应用实例：

```sh
$ kubectl run -i --tty --image busybox:1.28.4 dns-test --restart=Never --rm /bin/sh
$ nslookup web-0.nginx
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-0.nginx
Address 1: 10.244.1.7

$ nslookup web-1.nginx
Server:    10.0.0.10
Address 1: 10.0.0.10 kube-dns.kube-system.svc.cluster.local

Name:      web-1.nginx
Address 1: 10.244.2.7
```

这种方式，即使是 Pod 被重建，访问原先的 Pod 的名字也没问题。并且当 Pod 被删除，也会自动创建出新的 Pod。

这对于 **主从、主主、主备、一主多从等结构** 的拓扑关系是非常有帮助的。

#### 存储状态

StatefulSet 对存储状态的管理机制，主要使用的是 **Persistent Volume Claim(PVC)** 的功能。

只要在 StatefulSet yaml 中指定使用的 PVC，便会让 PVC 和 PV 绑定，这样即使当 Pod 被删除，重建时也会找到对应的 PVC 和 PV 绑定关系，原先的状态仍然可用，这样就实现了对应用存储状态的管理。

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.9.1
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes:
      - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
```

声明了 `persistentVolumeClaim` 的 PVC。

>  StatefulSet 为每一个 Pod 分配并创建一个同样编号的 PVC。这样，Kubernetes 就可以通过 Persistent Volume 机制为这个 PVC 绑定上对应的 PV，从而保证了每一个 Pod 都拥有一个独立的 Volume。在这种情况下，即使 Pod 被删除，它所对应的 PVC 和 PV 依然会保留下来。所以当这个 Pod 被重新创建出来之后，Kubernetes 会为它找到同样编号的 PVC，挂载这个 PVC 对应的 Volume，从而获取到以前保存在 Volume 里的数据。

### DaemonSet 

![](./images/daemonset.png)



DaemonSet 管理那些需要长时间运行的 Daemon 应用，主要是：

- 负责在每个节点上网了、存储等组件的运行，如 glusterd 、ceph、flannel 等
- 日志收集 Daemon，如 flunentd 或 logstash
- 监控 Daemon，如 Prometheus Node Exporter 或 collectd
- 监控节点状态，并上报给 APIServer

### Job 和 CronJob

有一类应用是属于一次性应用，区别于那些需要长时间运行的应用，这类应用运行一次便可以退出了，比如批处理程序，处理完就可以退出。

对这类应用是由 Job 来管理。

进一步，Job 根据任务的类型和执行的动作又分为以下几类：

- 单 Job 单任务：只启动一个 Job 来完成任务，同时 Job 只启用一个 Pod ，适用于简单的任务。
- 多 Job 多任务：启动多个 Job 来处理批量任务，每个任务对应一个 Job，Pod 的数量可以自定义。
- 单 Job 多任务：采用一个任务队列来存放任务，启动一个 Job 作为消费者来处理这些任务，Job 会启动多个 Pod，Pod 的数量可以自定义。
- 定时 Job：也叫 CronJob，启动一个 Job 来定时执行任务，类似 Linux 的 Crontab 程序。

## K8s 应用管理

### 应用升级(Rolling Update) 

即滚动更新。 **滚动更新的最大的好处是零停机，整个更新过程始终有副本在运行，从而保证了业务的连续性。**

#### 升级策略

每一种控制器支持的升级策略可能细节上不大一样，但原理是差不多的。



**Deployment支持的两种策略：**

- Recreate：即先删除现有的 Pod，再创建新的，不常用

- RollingUpdate：默认，即先创建一个新的 Pod，待其运行成功后，再删除旧的。Deployment Controller 会确保，在任何时间窗口内，只有指定比例的 Pod 处于离线状态，以及只有指定比例的新 Pod 被创建处理。这个比例默认是 DESIRED 的 25%，但可以设置。

在 yaml 文件配置的时候，通过下面两个参数控制

- `maxUnavailable` ：表示最多可以删除多少个旧的 Pod，取值可以是个数或百分比，下同
- `maxSurge`：表示最多可以创建多少个新的 Pod 

如下配置：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
...
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```



更多详细设置参考[这里](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#%E6%BB%9A%E5%8A%A8%E6%9B%B4%E6%96%B0-deployment)



**StatefulSet支持的两种升级策略**

- RollingUpdate：和 Deployment 一样，此外，还支持通过 partition 参数来分段更新一个 StatefulSet，所有序号大于或等于 partition 的 Pod 都将被更新
- OnDelete：先手动删除 Pod，才会触发新的 Pod 更新



#### 附：主流的升级发布方案

Deployment 可以用来轻松实现很多目前主流的发布模式，如金丝雀发布、蓝绿发布、以及 A/B 测试等等。这些问题[参考](https://github.com/ContainerSolutions/k8s-deployment-strategies/tree/master/canary)

- 金丝雀发布：优先发布一台或少量机器升级，等严重无误后再更新其他机器。优点是用户影响范围小，不足是要额外控制如何做自动更新。
- 蓝绿发布：2 组机器，蓝代表当前的 V1 版本，绿代表已经升级完成的 V2 版本。通过 LB 将流量全部导入 V2 完成升级部署。优点是切换快，缺点是影响全部用户。

Deployment 的滚动更新，是一个自动化更新的金丝雀发布。

### 应用回滚

应用如果更新失败，就需要回滚到以前的版本。

可以使用命令 `kubectl rollout undo`。

回滚到更早的版本，可以用 `kubectl rollout history` 查看，可以看到每次 Deployment 变更对应的版本。最好在创建 Deployment 的时候指定 `-record` 参数。

### 应用伸缩(扩展性)

即水平扩展、水平伸缩。只需要修改 Deployment 的 replicas（副本数）就可以了。有两种修改方式：

- kubectl scale 命令：（不推荐，社区已弃用）
- 修改 yaml 文件中的 replicas 值

### 应用容错(failover)



## K8s 任务调度



### 调度器

调度器的主要职责，就是为一个新创建出来的 Pod，寻找一个最合适的节点（Node）。

在具体的调度流程中，默认调度器会首先调用一组叫作 **Predicate** 的调度算法，来检查每个 Node。然后，再调用一组叫作 **Priority** 的调度算法，来给上一步得到的结果里的每个 Node 打分。最终的调度结果，就是得分最高的那个 Node。

https://time.geekbang.org/column/article/69890

调度器框架：

![](./images/schedulerarch.jpg)



### CPU和内存限制

#### requests + limits

#### QoS 模型

##### Guaranteed

requests 和 limits 相等

##### Burstable 

当 Pod 不满足 Guaranteed 的条件，但至少有一个 Container 设置了 requests。那么这个 Pod 就会被划分到 Burstable 类别。

##### BestEffort

如果一个 Pod 既没有设置 requests，也没有设置 limits，那么它的 QoS 类别就是 BestEffort。

#### 资源回收

即 Eviction，分为 Soft 和 Hard 两种模式。

- Soft Eviction 允许你为 Eviction 过程设置一段“优雅时间”，比如上面例子里的 imagefs.available=2m，就意味着当 imagefs 不足的阈值达到 2 分钟之后，kubelet 才会开始 Eviction 的过程。
- 而 Hard Eviction 模式下，Eviction 过程就会在阈值达到之后立刻开始。

#### cpuset



### 调度器算法

#### Predicates(过滤)

##### GeneralPredicates

##### 与 Volume 相关

##### 与宿主机相关

##### 与 Pod 相关



#### Priorities(打分)

**LeastRequestedPriority：**

选择空闲资源（CPU 和 Memory）最多的宿主机

```sh
score = (cpu((capacity-sum(requested))10/capacity) + memory((capacity-sum(requested))10/capacity))/2
```



**BalancedResourceAllocation：**

选择所有节点里各种资源分配最均衡的那个节点

```sh
score = (cpu((capacity-sum(requested))10/capacity) + memory((capacity-sum(requested))10/capacity))/2
```



此外，还有 NodeAffinityPriority、TaintTolerationPriority 和 InterPodAffinityPriority 这三种 Priority。



### 优先级调度

当一个高优先级的 Pod 调度失败后，该 Pod 并不会被“搁置”，而是会“挤走”某个 Node 上的一些低优先级的 Pod 。这样就可以保证这个高优先级 Pod 的调度成功。

https://time.geekbang.org/column/article/70519

#### Taint/Toleration

污点/容忍调度。原理是：一旦某个节点被加上了一个 Taint，即被“打上了污点”，那么所有 Pod 就都不能在这个节点上运行，因为 Kubernetes 的 Pod 都有“洁癖”。

除非，有个别的 Pod 声明自己能“容忍”这个“污点”，即声明了 Toleration，它才可以在这个节点上运行。

打污点命令：

```sh
$ kubectl taint nodes node1 foo=bar:NoSchedule
```

声明 Toleration，则在 Pod 的 yaml 文件中 spec 部分，加入 tolerations 字段即可：

```yaml
apiVersion: v1
kind: Pod
...
spec:
  tolerations:
  - key: "foo"
    operator: "Equal"
    value: "bar"
    effect: "NoSchedule"
```

这个 Toleration 的含义是，这个 Pod 能“容忍”所有键值对为 foo=bar 的 Taint（ operator: “Equal”，“等于”操作）。

一个典型的例子就是 Master 节点默认是被打上污点的：

```sh
$ kubectl describe node master

Name:               master
Roles:              master
Taints:             node-role.kubernetes.io/master:NoSchedule
```

除了设置 toleration，还可以直接删除该污点，如如果想让 Master 当作 Node 来调度，可以删除：

```sh
# 短线 - 表示删除以“node-role.kubernetes.io/master”为键的 Taint
$ kubectl taint nodes --all node-role.kubernetes.io/master-
```

想要恢复，再：

```sh
kubectl taint node k8s-master node-role.kubernetes.io/master="":NoSchedule
```

## 04 K8S 的扩展性（应用伸缩）

K8S 的伸缩功能是指在线 **增加和减少** Pod 的副本数。

这又分为 **手动伸缩** 和 **自动伸缩** 。

**手动伸缩：**

在 yaml 文件中指定副本数 `replicas`，然后通过 命令 `kubectl apply -f xx.yml` 完成副本的伸缩。

**自动伸缩：**

K8S 的自动伸缩功能，叫做 Horizontal Pod Autoscaling，HPA，也是一种 K8S 的资源对象。

HPA 的目标是希望通过追踪集群中所有 Pod 的负载变化情况，来自动化地调整 Pod 的副本数，以此来满足应用的需求和减少资源的浪费。

HAP 度量 Pod 负载变化情况的指标有两种：

- CPU 利用率（CPUUtilizationPercentage）
- 自定义的度量指标，比如服务在每秒之内的请求数（TPS 或 QPS）

HPA 实现的方式有两种：

- yaml 配置文件
- 命令行

比如通过下面的命令行 `kubectl autoscale`：

```
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

## 05 K8S 的容错性（failover）

一台主机关闭之后，上面的 Pod 会变为 unknown，并在另外的主机上启用新的 Pod，始终维持 Pod 的副本数在期望状态。



## K8s 容器运行时

### CRI

CRI 为容器运行时接口，相当于容器运行时为接入 K8s 做的一层抽象，K8s 通过 CRI 支持多种容器运行时，各家容器厂商或开发者想接入自己的容器运行时，只需要根据 k8s 提供的 CRI 接口实现相关的组件即可，该组件主要做的事就是将 kubelet 的 CRI 请求转换到具体的容器运行时进行处理。

这样一个组件一般被称为 CRI shim：实现 CRI 规定的每个接口，然后把具体的 CRI 请求转换成对后端容器运行时的请求或操作。一般都需要在每台宿主机上单独安装。



目前实现的比较流行的组件有：

- dockershim：Docker 提供，负责将请求发给 docker daemon，
- rkt：CoreOS 公司开发
- containerd：CNCF 项目，基础了 containerd-shim
- frakti：https://github.com/kubernetes/frakti
- CRI-O：k8s 自己实现的



### OCI

具体的容器运行时，一般要遵循 **OCI** （Open Container Interface，开放容器接口，为 CRI 遵循的规范）容器运行时规范来创建容器，它的本质是同底层的 Linux 操作系统进行交互，即把 CRI 的请求翻译成对 Linux 操作系统的调用（具体就是 namespace 和 cgroup 的调用）。

简单说只要符合这个标准运行的，就是容器。那么目前符合这个标准的容器运行时有：

- Kata、gVisor：专注安全容器
- runC：CNCF 项目 containerd 作用的 runtime
- runV：Kata Containers 的前身



CRI 和 OCI 的关系可以用下图表示。

![](./images/crioci.png)



kubelet、CRI、OCI 三者之间的关系：

![](./images/kubectlcri.jpg)



### containerd

以 CNCF 项目的 containerd 举个例子，如下：

- containerd：提供 CRI shim 能力，将 kubelet CRI 请求转换成 containerd 调用，创建 runC 容器
- runC ：负责设置容器 Namespace、Cgroups 和 chroot 等基础操作的组件

![](./images/containerd.png)



### Kata Containers 和 gVisor

本质：

给进程分配一个独立的操作系统内核，避免共享宿主机内核。

这个操作系统内核是一个极小的、独立的、以容器为单位的内核。

- Kata Containers 使用传统虚拟化技术，通过虚拟硬件模拟出了一台小虚拟机，然后在小虚拟机里安装一个裁剪后的 Linux 内核实现强隔离
- gVisor：Google 实现，直接使用 Go 语言模拟出了一个运行在用户态的操作系统内核



## K8s 网络

### Docker 网络

单主机网络方案：

- host
- none
- bridge：docker0
- container：使用别的容器的网络

使用方法：启动容器的时候 使用 `--network` 指定，不指定默认使用 bridge 网络

```sh
$ docker run –it –-network=none busybox
$ docker run –it –-network=host busybox
$ docker run –it –-network=mynet busybox
```

跨主机网络方案：和 K8s 网络方案是一样的，使用各种网络插件，见下面的分析。

### CNI

Container Network Interface，即容器网络接口，是当前容器网络的事实标准。K8s 支持多种容器网络插件，它们就是通过 CNI 接入到 K8s 中的。

目前市面上流行的容器网络插件有：

- Flannel
- Calico
- Canal
- Weave
- Romana
- Contiv-vpp
- ......

CNI 和 Docker 容器提出的 CNM（Container Network Model）具有异曲同工之处。docker0 网桥在 K8s 的 CNI 中叫 CNI 网桥，名称为 cni0。

#### CNI 的设计

**设计思想：** K8s 在启动 Infra 容器之后，就可以直接调用 CNI 网络插件，为这个 Infra 容器的 Network Namespace，配置符合预期的网络栈。

#### CNI 的实现

1、首先，安装 Kubernetes 的时候，会安装 Kubernetes-cni 包，这个包提供了 **CNI 插件所需要的基础可执行程序** 。在 `/opt/cni/bin` 可以看到：

```sh
$ ls -al /opt/cni/bin/
total 73088
-rwxr-xr-x 1 root root  3890407 Aug 17  2017 bridge
-rwxr-xr-x 1 root root  9921982 Aug 17  2017 dhcp
-rwxr-xr-x 1 root root  2814104 Aug 17  2017 flannel
-rwxr-xr-x 1 root root  2991965 Aug 17  2017 host-local
-rwxr-xr-x 1 root root  3475802 Aug 17  2017 ipvlan
-rwxr-xr-x 1 root root  3026388 Aug 17  2017 loopback
-rwxr-xr-x 1 root root  3520724 Aug 17  2017 macvlan
-rwxr-xr-x 1 root root  3470464 Aug 17  2017 portmap
-rwxr-xr-x 1 root root  3877986 Aug 17  2017 ptp
-rwxr-xr-x 1 root root  2605279 Aug 17  2017 sample
-rwxr-xr-x 1 root root  2808402 Aug 17  2017 tuning
-rwxr-xr-x 1 root root  3475750 Aug 17  2017 vlan
```

可以看到，基本上是 Kubernetes 网络所需的网络驱动，复杂的网络插件，如 flannel、calico 等都需要依赖这些基本的驱动。

总体可以分为三个部分：

- main 插件：创建具体网络设备，如bridge（网桥设备）、ipvlan、loopback（lo 设备）、macvlan、ptp（veth pair设备），以及 vlan
- IPAM 插件：负责 IP 地址分配，如 dhcp（动态分配地址）、host-local（使用预先分配的地址段）
- CNI 社区维护的内置 CNI 插件：如 flannel（专门为 flannel 项目提供）、tuning（通过 sysctl 调整网络设备参数）、portmap（通过 iptables 配置端口映射）、bandwidth（使用 Token Bucket Filter(TBF) 来进行限流）等

一个具体的网络插件，都需要依赖这些 CNI 插件来实现。以 Flannel 来举个例子：

分为两个阶段：

- 实现网络方案本身
- 实现网络方案对应的 CNI 插件

2、实现网络方案

需要实现诸如 flanneld 一样的进程，负责创建和配置 flannel.1 设备、配置宿主机路由、配置 ARP 和 FDB 表等信息

3、实现网络方案对应的 CNI 插件

主要是配置 Infra 容器里的网络栈，把它连接到 CNI 网桥上。

1）安装相应的网络插件的可执行程序到 `/opt/cni/bin` 下

2）生成 CNI 配置文件到 `/etc/cni/net.d` 下，如 flannel 就是 `10-flannel.conflist`

3）kubelet 里 的 CRI 会加载该 CNI 配置，如果是 Docker，CRI 就是 dockershim。

#### CNI 工作原理

kubelet 创建 Pod-》dockershim 创建并启动 Infra 容器-》执行 SetUpPod 方法，为 CNI 擦肩准备参数，调用 CNI 插件为 Infra 容器配置网络。

CNI 插件主要实现两个方法：ADD 和 DEL

> ADD 操作的含义是：把容器添加到 CNI 网络里；DEL 操作的含义则是：把容器从 CNI 网络里移除掉。而对于网桥类型的 CNI 插件来说，这两个操作意味着把容器以 Veth Pair 的方式“插”到 CNI 网桥上，或者从网桥上“拔”掉。

详细参考：https://time.geekbang.org/column/article/67351

### NetworkPolicy

用于 K8s 网络隔离。K8s 中 Pod 默认都是连通的，如果需要限制连通，需要通过 NetworkPolicy 来限制。

目前 CNI 插件实现 Network Policy 的有：

- Calico
- Weave
- kube-router

对于不支持 NetworkPolicy 的插件来说，如果要使用，需要再安装一个支持的插件，如 Flannel 就需要额外安装 Calico，[这里](https://docs.projectcalico.org/v3.2/getting-started/kubernetes/installation/flannel)有一键安装的方法。

#### 实现机制

K8s 将用户定义的 NetworkPolicy  yaml 文件中定义的白名单规则转换为 iptables 规则来实现的。

一般会有两组规则，第一组拦截对 Pod 的请求，并将请求转到第二组规则，第二组规则是 NetworkPolicy 自定义的 iptables 规则。

NetworkPolicy yaml 文件例子如下：

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

解释：https://time.geekbang.org/column/article/68316

### Service 网络

#### 实现机制

有两种实现方式：

- kube-proxy + iptables。缺点：iptables 规则多，影响性能
- kube-proxy + IPVS 模式。Kube-proxy 会创建一个虚拟网卡（kube-ipvs0），并分配 VIP 地址，然后利用 Linux 的 IPVS 模块，设置相应的 IPVS 虚拟主机，即 Service 代理的 Pod。通过 设置 kube-proxy --proxy-mode=ipvs 来开启 ipvs 功能

### Ingress

Ingress 服务：全局的、代理不同后端 Service 而设置的负载均衡服务，就是 service 的 service。

本质是一个反向代理。并且提供 Ingress Controller 可以选择对应的 Ingress 对象，目前业界常用的各种反向代理项目：Nginx、HAProxy、Envoy、Traefik 等，都已经为 Kubernetes 专门维护了对应的 Ingress Controller。

如 Nginx Ingress Controller：

```sh
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/mandatory.yaml
```

Ingress 工作在七层，而 Service 工作在 四层。所以当你想要在 Kubernetes 里为应用进行 TLS 配置等 HTTP 相关的操作时，都必须通过 Ingress 来进行。

### Flannel

Flannel 是 CoreOS 公司推出的容器网络方案，也是目前最为简单，也最为主流的方案，它提供了三种后端实现：

- VxLAN
- host-gw
- UDP

#### Subnet

Flannel 一个关键的实现是需要分配一个 Subnet（子网），这个子网，是供 Flannel 维护为集群中的 Pod 分配 IP 的，一般每个 Node 会从 Subnet 中再划分一个子网来作为自身 Pod 的分配。比如 Node1 是 `10.10.1.0/24`，Node2 是 `10.10.2.0/24`。

指定子网，通过初始化集群的时候完成，如：

```sh
kubeadm init --pod-network-cidr=10.244.0.0/16
```

#### UDP

UDP 模式，是 Flannel 项目最早支持的一种方式，却也是性能最差的一种方式。所以， **这个模式目前已经被弃用** 。

为什么它的性是最差的？如下：

![](./images/flanneludp.jpg)

![](./images/flanneludp1.png)

可以看到：

- 将数据发出去，需要经过三次用户态和内核态的切换和数据拷贝，以及 UDP 包在用户态的封装和解封装
- flannel0 是一个 TUN 设备（三层设备，用于内核和应用程序之间传递 IP 包）
- flanneld 是一个用户态的服务进程，用于路由和封装解封装等操作，它监听 8285 端口

flanneld 是如何知道其他 Node 的 IP 的？答案是通过 etcd。

etcd 会保存子网和 Node 的关系：

```sh
$ etcdctl ls /coreos.com/network/subnets
/coreos.com/network/subnets/100.96.1.0-24
/coreos.com/network/subnets/100.96.2.0-24
/coreos.com/network/subnets/100.96.3.0-24
```

根据子网找到 Node IP：

```sh
$ etcdctl get /coreos.com/network/subnets/100.96.2.0-24
{"PublicIP":"10.168.0.3"}
```

不过现在最新版本好像都不用 etcd 来维护了，直接通过 api-server 来维护。

#### VxLAN

VxLAN 模式改善了 UDP 模式，因为 VxLAN 属于内核的一个模块，所以封装解封装等操作都在内核完成，减少了内核和用户态的切换，如下：

![](./images/flannelvxlan.jpg)

![](./images/flannelvxlan1.png)

可以看到：

- VxLAN 模式只有一次内核态和用户态的切换和数据拷贝
- flannel.1 是 VxLAN 的 VTEP 设备，1 表示 VNI 为1，flannel 中 VNI 的默认值为1，所以叫 flannel.1
- api-server 充当 VxLAN 的控制面，同步信息，不需要etcd 的支持，具体地，各个 node 会将自己的 vtep 信息上报给 api-server，api-server 将信息同步给各个 Node 的flanneld，flanneld 通过 netlink 下发，完成信息的同步

#### host-gw

顾名思义，host-gw 方案是将主机充当容器通信路径里的网关。

具体是将 Flannel 的每个子网的下一跳，设置成目标子网所在的宿主机的 IP 地址。比如，子网 10.244.0.0/24 去往子网 10.244.1.0/24 的流量，下一跳为 目标子网所在宿主机的 10.168.0.3/24。

```sh
$ ip route
...
10.244.1.0/24 via 10.168.0.3 dev eth0
```

![](./images/hostgw.png)

可以看到，host-gw 将目标主机写入本机的路由表，意味着使用下一跳的MAC 地址是可达的。这就要求： **集群中的宿主机之间必须二层互通的。**

有没有不用要求二层互通的方案，有，就是 Calico。

#### 方案对比

- host-gw 的性能损失大约在 10% 左右
- 基于 VXLAN“隧道”机制的网络方案，性能损失都在 20%~30% 左右

### Calico

#### calico 和 flannel host-gw 的异同

- 相同：都将目标主机写入本机的路由，让容器直接找到下一跳通信
- 不同：
	- Flannel 通过 etcd 和宿主机上的 flanneld 来维护路由信息；Calico 通过 BGP
	- Calico 不会创建任何网桥设备，如 cni0，而是通过一个 veth-pair 设备来连接宿主机和容器

![](./images/calico.jpg)



#### Calico 的组件

- CNI 插件：与 K8s 对接
- Felix：负责设置路由表和 ACL 规则等，同时它还负责提供有关网络健康状况的数据，并写入 etcd，是一个 DaemonSet
- BIRD：BGP 客户端，负责使用 BGP 协议分发和同步集群中的路由规则信息

#### node-to-node mesh

每台宿主机上的 BGP client 地位是平等的，彼此交换路由信息：

```sh
[BGP消息]
我是宿主机192.168.1.3
10.233.2.0/24网段的容器都在我这里
这些容器的下一跳地址是我
```

随着集群 node 的增加，将会给集群网络管理带来很大的压力。

#### Route Reflector

有一个或几个专门的节点来充当 Master，学习全局的路由规则，其他节点只需要跟 Master 节点交换路由信息，就可以拿到整个集群的信息。

#### IPIP 模式解决跨二层的通信

![](./images/calicoipip.jpg)

这时的路由规则是：

```sh
10.233.2.0/24 via 192.168.2.2 tunl0
```

tunl0 是一个 IP tunnel 设备。

IP 包进入 IP 隧道设备之后，就会被 Linux 内核的 IPIP 驱动接管。IPIP 驱动会将这个 IP 包直接封装在一个宿主机网络的 IP 包中，如下所示：

![](./images/ipip.jpg)

**缺点：**

封包和解包影响网络性能。

在实际测试中，Calico IPIP 模式与 Flannel VXLAN 模式的性能大致相当，在实际使用时，如非硬性需求，我建议你将所有宿主机节点放在一个子网里，避免使用 IPIP。

> 在大多数公有云环境下，宿主机（公有云提供的虚拟机）本身往往就是二层连通的，所以这个需求也不强烈。

#### 私有部署避免使用 IPIP 的方案

公有云环境集群主机基本是二层互通的，也不让动宿主机之间的网关。但在私有部署环境下，比如宿主机属于不同子网（VLAN）是比较常见的需求。

这时 Calico 提供了两种方案，可以使用 **宿主机的网关** 来设置成 BGP Peer，加入主机的路由规则中。

- 方案一：要求网关支持 Dynamic Neighbors 的 BGP 配置方式，即给路由器配置一个网段，然后路由器就会自动跟该网段里的主机建立 BGP Peer 关系。不推荐
- 方案二：Route Reflector 负责将集群路由信息同步给网关，无需 Dynamic Neighbors 支持

#### calicoctl

Calico 提供的 CLI 工具。

安装：

```bash
$ wget https://github.com/projectcalico/calicoctl/releases/download/v3.15.0/calicoctl -O /usr/local/bin/calicoctl
$ chmod +x /usr/local/bin/calicoctl
```



### weave

部署命令：

```sh
$ kubectl apply -f https://git.io/weave-kube-1.6
```

## K8s 存储

容器的生命周期是短暂的，因此需要将它的数据绑定到相应的存储系统Volume中，如：

![](./images/types-of-mounts-volume.png)



K8s 中 Volume 是和 Pod 进行绑定的：

![](./images/k8svolume.png)



### Volume

Volume 机制就是将一个宿主机上的目录，跟一个容器里的目录绑定挂载在一起。这样，容器里读写该文件，也会反映到宿主机上。

使用的时候，分为两步，首先声明宿主机上的 Volume 目录，然后，在容器中声明对应的挂载方式。

在 K8s 中，Volume 是属于 Pod 对象的一部分，所以，要在一个 Pod 里声明 Volume，只要修改 Pod 里 template.spec.volumes 字段即可（在这个字段里定义一个具体类型的 Volume）。

而 Pod 中的容器，使用 `volumeMounts` 字段来声明自己要挂载哪个 Volume，并通过 `mountPath` 字段来定义容器内的 Volume 目录。

K8s 提供了两类 Volume：

- 本地 Volume：本地文件存储，非持久化
	- **hostPath：** 显示声明宿主机目录的 Volume
	- **emptyDir：** 隐式声明宿主机目录的 Volume，即 K8s 会创建一个临时目录作为该 Volume 的宿主机目录，这么做的原因是：K8s 不想依赖 Docker创建的那个 `_data` 目录
- 持久化 Volume：借助各种存储插件实现远程存储，持久化
	- **PVC 和 PV**

#### hostPath

hostPath 声明方式如下 yaml 文件：

```yaml
 ...   
    volumes:
      - name: nginx-vol
        hostPath: 
          path:  " /var/data"
```

这里声明了使用宿主机上的目录： `/var/data`，接下来看看容器如何挂载使用：

一个 Pod 中两个容器共享同一个 Volume 的例子：

```sh
apiVersion: v1
kind: Pod
metadata:
  name: two-containers
spec:
  restartPolicy: Never
  volumes:
  - name: shared-data
    hostPath:      
      path: /data
  containers:
  - name: nginx-container
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
  - name: debian-container
    image: debian
    volumeMounts:
    - name: shared-data
      mountPath: /pod-data
    command: ["/bin/sh"]
    args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
```

可以看到，nginx-container 和 debian-container 同时共享了 shared-data 这个 hostPath Volume，这样，nginx-container 可以从它的 `/usr/share/nginx/html` 目录中，读取到 debian-container 生成的 `index.html` 文件。

#### emptyDir 

下面再看看 emptyDir 的 yaml 文件：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
        volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: nginx-vol
      volumes:
      - name: nginx-vol
        emptyDir: {}
```

这里指定了一个空的 `emptyDir: {}` 。

当创建 Pod 之后，kubectl describe 看到该 Volume：

```sh
...
Containers:
  nginx:
    Container ID:   docker://07b4f89248791c2aa47787e3da3cc94b48576cd173018356a6ec8db2b6041343
    Image:          nginx:1.8
    ...
    Environment:    <none>
    Mounts:
      /usr/share/nginx/html from nginx-vol (rw)
...
Volumes:
  nginx-vol:
    Type:    EmptyDir (a temporary directory that shares a pod's lifetime)
```

#### PVC 和 PV

上述的 hostPath 和 emptyDir 类型的 Volume 不具备持久化的特性：因为它们既有可能被 kubelet 清理掉，也不能迁移到其他节点上。PVC 和 PV 就可以提供持久化的功能。

- PV：Persistent Volume，持久化存储的 Volume，支持多种存储插件，如 NFS、Ceph 等
- PVC：Persistent Volume Claim，描述了 Pod 所希望使用的持久化存储的属性，比如 Volume 存储的大小、可读写权限等，需要和 PV 绑定使用



**总结：** PVC 提供了对某种持久化存储的描述，但不提供具体的实现；而这个持久化存储的实现部分则由 PV 负责完成。



**这样做的好处是：** 作为应用开发者，我们只需要跟 PVC 这个“接口”打交道，而不必关心具体的实现是 NFS 还是 Ceph。毕竟这些存储相关的知识太专业了，应该交给专业的人去做。



可以理解 PV 是对实际物理存储资源的描述，PVC 是便于使用的抽象 API，一般都是在 Pod 中通过 PVC 的方式来使用 PV，如：

![](./images/pvcpv.png)



##### 创建 PV 的方式

创建 PV 有两种方式：静态和动态，如下：

![](./images/createpv.jpg)



**静态 PV：**

管理员通过手动的方式在后端存储平台上创建好对应的 Volume，然后通过 PV 定义到 Kubernetes 中去。开发者通过 PVC 来使用：

![](./images/staticpv.png)



**动态 PV：**

使用 StorageClass。

![](./images/dynamicpv.png)



##### Volume Controller

属于 kube-controller manager 的一部分，K8s 中专门处理持久化存储的控制器，维护着多个控制循环，其中有一个循环，专门处理 PVC 和 PV 的联系，它的名字叫作  **PersistentVolumeController** 。



比如当前没有合适的 PV ，那么等有了之后（运维人员紧急创建的），PersistentVolumeController 会自动监听并完成绑定关系。



具体地，PersistentVolumeController 会不断地查看当前每一个 PVC，是不是已经处于 Bound（已绑定）状态。如果不是，那它就会遍历所有的、可用的 PV，并尝试将其与这个“单身”的 PVC 进行绑定。这样，Kubernetes 就可以保证用户提交的每一个 PVC，只要有合适的 PV 出现，它就能够很快进入绑定状态，从而结束“单身”之旅。



##### attach and mount



##### StorageClass

StorageClass 对象的作用，其实就是创建 PV 的模板。



#### Projected Volume

译为投射数据卷。是 Kubernetes v1.11 之后的新特性。

该种 Volume 不是用来存放容器里的数据，也不是用来进行容器和宿主机之间的数据交换，而是为容器提供预先定义好的数据，从容器的角度来看，这些数据就好像是被 K8s “投射”（project）进入容器中的。

目前，Kubernetes 支持的 Projected Volume 一共有四种：

- Secret
- ConfigMap
- Downward API
- ServiceAccountToken



其中，Secret 和 ConfigMap 分别用于配置敏感需要加密的数据，和非敏感数据。 **是 Pod 配置管理非常重要的两个对象。**

![](./images/configmap.gif)



##### Secret

见 K8s 安全中 Secret 对象部分

##### ConfigMap

保存容器应用所需的配置信息，用法和 Secret 类似，但不需要加密。

比如保存一个 Java 应用所需的配置文件（.properties 文件）：

```sh
# .properties文件的内容
$ cat example/ui.properties
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice

# 从.properties文件创建ConfigMap
$ kubectl create configmap ui-config --from-file=example/ui.properties

# 查看这个ConfigMap里保存的信息(data)
$ kubectl get configmaps ui-config -o yaml
apiVersion: v1
data:
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  name: ui-config
  ...
```

##### Downward API

作用是 Pod 里的容器直接获取到  Pod API 对象本身的信息。包括

- 宿主机的信息：名字、IP 等
- Pod 的信息：名字、IP、namespace 等

如下：

```sh
# 1. 使用fieldRef可以声明使用:
spec.nodeName - 宿主机名字
status.hostIP - 宿主机IP
metadata.name - Pod的名字
metadata.namespace - Pod的Namespace
status.podIP - Pod的IP
spec.serviceAccountName - Pod的Service Account的名字
metadata.uid - Pod的UID
metadata.labels['<KEY>'] - 指定<KEY>的Label值
metadata.annotations['<KEY>'] - 指定<KEY>的Annotation值
metadata.labels - Pod的所有Label
metadata.annotations - Pod的所有Annotation

# 2. 使用resourceFieldRef可以声明使用:
容器的CPU limit
容器的CPU request
容器的memory limit
容器的memory request
```

> 上面这个列表的内容，随着 Kubernetes 项目的发展肯定还会不断增加。所以这里列出来的信息仅供参考，你在使用 Downward API 时，还是要记得去查阅一下官方文档。
>
> 另外，需要注意的是，Downward API 能够获取到的信息，一定是 Pod 里的容器进程启动之前就能够确定下来的信息。而如果你想要获取 Pod 容器运行后才会出现的信息，比如，容器进程的 PID，那就肯定不能使用 Downward API 了，而应该考虑在 Pod 里定义一个 sidecar 容器。

##### Service Account

这是一种特殊的 Secret 对象，叫做 ServiceAccountToken，是 K8s 进行权限分配的对象。它提供了访问 K8s api-server 的授权信息和文件，只有得到了这些信息，任何运行在 K8s 集群上的应用才能合法访问 API server。

为了方便使用，Kubernetes 已经为你提供了一个默认“服务账户”（default Service Account）。并且，任何一个运行在 Kubernetes 里的 Pod，都可以直接使用这个默认的 Service Account，而无需显示地声明挂载它。如下：

```sh
$ kubectl describe pod nginx-deployment-5c678cfb6d-lg9lw
Containers:
...
  Mounts:
    /var/run/secrets/kubernetes.io/serviceaccount from default-token-s8rbq (ro)
Volumes:
  default-token-s8rbq:
  Type:       Secret (a volume populated by a Secret)
  SecretName:  default-token-s8rbq
  Optional:    false
```

可以看到，这个 Secret 类型的 Volume，正是默认 Service Account 对应的 ServiceAccountToken。

Pod 里的容器可以从文件 `/var/run/secrets/kubernetes.io/serviceaccount` 取到 token 信息：

```sh
$ ls /var/run/secrets/kubernetes.io/serviceaccount 
ca.crt namespace  token
```

当然，除了默认的 Service Account 外，我们也可以自定义自己的 Service Account，来对应不同的权限设置。这样，我们的 Pod 里的容器就可以通过挂载这些 Service Account 对应的 ServiceAccountToken，来使用这些自定义的授权信息。

###### InClusterConfig

上面这种把 Kubernetes 客户端（如 `k8s.io/client-go`）以容器的方式运行在集群里，然后使用 default Service Account 自动授权的方式，被称作 **InClusterConfi** ，是最推荐的进行 Kubernetes API 编程的授权方式。

### CSI

CSI，Container storage interface，即容器存储接口，可以接入多种容器存储插件，为 K8s 集群提供持久化存储。



**容器持久化存储的意义：**

如果你在某一台机器上启动的一个容器，显然无法看到其他机器上的容器在它们的数据卷里写入的文件。 **这是容器最典型的特征之一：无状态。**

而容器的持久化存储，就是用来保存容器存储状态的重要手段。

持久化 Volume 的实现，往往依赖于一个远程存储服务，比如：远程文件存储（比如，NFS、GlusterFS）、远程块存储（比如，公有云提供的远程磁盘）等等。

![](./images/k8sremotevolume.png)



存储插件会在容器里挂载一个基于网络或者其他机制的远程数据卷，使得在容器里创建的文件，实际上是保存在远程存储服务器上，或者以分布式的方式保存在多个节点上，而与当前宿主机没有任何绑定关系。这样，无论你在其他哪个宿主机上启动新的容器，都可以请求挂载指定的持久化存储卷，从而访问到数据卷里保存的内容。 **这就是“持久化”的含义。**



K8s 支持的持久化存储插件有：

- Ceph
- GlusterFS
- NFS
- Rook

详细见下表：

![](./images/k8svolumeplugin.png)

更多插件见：

https://kubernetes-csi.github.io/docs/drivers.html

如何开发一个 CSI driver：

https://kubernetes-csi.github.io/docs/introduction.html



### Rook

一个基于 Ceph 的存储插件。Rook 封装了 Ceph，并在自己的实现中加入了水平扩展、迁移、灾难备份、监控等大量的企业级功能，使得这个项目变成了一个完整的、生产级别可用的容器存储插件。

Rook 以容器的方式运行在 K8s 集群中。

用几条指令，Rook 就可以把复杂的 Ceph 存储后端部署起来：

```sh
$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/common.yaml

$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml

$ kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml
```

在部署完成后，你就可以看到 Rook 项目会将自己的 Pod 放置在由它自己管理的两个 Namespace 当中：

```sh
$ kubectl get pods -n rook-ceph-system
NAME                                  READY     STATUS    RESTARTS   AGE
rook-ceph-agent-7cv62                 1/1       Running   0          15s
rook-ceph-operator-78d498c68c-7fj72   1/1       Running   0          44s
rook-discover-2ctcv                   1/1       Running   0          15s

$ kubectl get pods -n rook-ceph
NAME                   READY     STATUS    RESTARTS   AGE
rook-ceph-mon0-kxnzh   1/1       Running   0          13s
rook-ceph-mon1-7dn2t   1/1       Running   0          2s
```

这样，一个基于 Rook 持久化存储集群就以容器的方式运行起来了，而接下来在 Kubernetes 项目上创建的所有 Pod 就能够通过 Persistent Volume（PV）和 Persistent Volume Claim（PVC）的方式，在容器里挂载由 Ceph 提供的数据卷了。

之后，Rook 则会负责这些数据卷的生命周期管理、灾难备份等运维工作。

**为什么 Rook 会这么出名？**

原因是：它巧妙地依赖了 Kubernetes 提供的编排能力，合理的使用了很多诸如 Operator、CRD 等重要的扩展特性。这使得 Rook 项目，成为了目前社区中基于 Kubernetes API 构建的最完善也最成熟的容器存储插件。



## K8s 其他插件

除了上述的 CRI、CNI、CSI 插件，k8s 还可以扩展其他的插件，如 GPU 相关的 Device Plugin 等。



### Device Plugin

Device Plugin 是 Kubernetes 项目用来管理 GPU 等宿主机物理设备的主要组件，也是基于 Kubernetes 项目进行机器学习训练、高性能作业支持等工作必须关注的功能。

kubelet 会通过 gRPC 协议同一 Device Plugin 插件进行交互，以便操作 GPU 等资源的管理。



## K8s 安全

一个 K8s 要生产可用，就必须 **具备身份认证和权限授权能力** 。一般来说，各内部组件之间相互通信会采用自签名的 TLS 证书，通过 HTTPS 来加强安全访问。同时，为了能够确定各自的身份和权限，常常借助于 mTLS （mutual TLS，双向 TLS）认证。



### API server 访问证书

Kubernetes 对外提供服务时，除非专门开启“不安全模式”，否则都要通过 HTTPS 才能访问 `kube-apiserver`。这就需要为 Kubernetes 集群配置好证书文件。

而这个证书是在 kubeadm 部署集群的时候就生成的，生成的证书文件都放在 Master 节点的 `/etc/kubernetes/pki` 目录下。在这个目录下，最主要的证书文件是 `ca.crt` 和对应的私钥 `ca.key`。

此外，kubectl 工具的操作需要双向认证，同样也需要相应的证书。比如，用户使用 kubectl 获取容器日志等 streaming 操作时，需要通过 kube-apiserver 向 kubelet 发起请求，这个连接也必须是安全的。kubeadm 为这一步生成的是 `apiserver-kubelet-client.crt` 文件，对应的私钥是 `apiserver-kubelet-client.key`。

除此之外，Kubernetes 集群中还有 Aggregate APIServer 等特性，也需要用到专门的证书。

### token

token 相当于当前部署集群的一把钥匙，你需要加入集群中，就需要有这把钥匙，典型的就是：使用 kubeadm 部署集群的时候，kubeadm init 在 Master 节点上生成 token，再 Node 上通过 kubeadm join + token 加入。

Node 本质上，拿到 token，是为了拿到访问 api-server 的的证书文件（CA文件），证书文件是在 kubeadm init 的时候，以 ConfigMap 的形式保存在 Master 上的 etcd 中。

所以，在 Node 上，kubeadm join 的时候，至少需要发起一次“不安全模式”的访问到 kube-apiserver，从而拿到保存在 ConfigMap 中的 证书文件才行。

ConfigMap 其实保存了集群信息，即 cluster-info，其中保存了kube-apiserver 的地址、端口、证书。

### Secret 对象

作用是把 Pod 想要访问的加密数据，存放到 etcd 中，提供集群中不同 Pod 之间的授权访问信息。你可以通过在 Pod 的容器里挂载 Volume 的方式，访问到这些 Secret 里保存的信息。

最典型的例子就是存放数据库需要的 Credential 信息（数据库的用户名和密码），然后 Web 应用访问时使用。

具体地，K8s 会将数据库的 Credential 信息以 Secret 的方式存在 Etcd 里，当 Web 应用的 Pod 启动时，K8s 会自动把 Secret 里的数据以 Volume 的方式挂载到容器里。这样，这个 Web 应用就可以访问数据库了。

例子：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-projected-volume 
spec:
  containers:
  - name: test-secret-volume
    image: busybox
    args:
    - sleep
    - "86400"
    volumeMounts:
    - name: mysql-cred
      mountPath: "/projected-volume"
      readOnly: true
  volumes:
  - name: mysql-cred
    projected:
      sources:
      - secret:
          name: user
      - secret:
          name: pass
```

如上声明 Projected 类型的 Volume，这个 Volume 的数据来源（sources），则是名为 user 和 pass 的 Secret 对象，分别对应的是数据库的用户名和密码。这里用到的数据库的用户名、密码，正是以 Secret 对象的方式交给 Kubernetes 保存的。

完成这个操作的指令，有两种方法：

（1）使用 `kubectl create secret`

```sh
$ cat ./username.txt
admin
$ cat ./password.txt
c1oudc0w!

$ kubectl create secret generic user --from-file=./username.txt
$ kubectl create secret generic pass --from-file=./password.txt
```

（2）使用 yaml 文件

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  user: YWRtaW4=
  pass: MWYyZDFlMmU2N2Rm
```

**注意：** 其中 user 和 pass 必须经过 Base64 转码，以免出现明文密码的安全隐患，比如上面的转换是：

```sh
$ echo -n 'admin' | base64
YWRtaW4=
$ echo -n '1f2d1e2e67df' | base64
MWYyZDFlMmU2N2Rm
```

**再注意：** 以上创建的 Secret 对象，仅仅是经过转码，而没有加密，这在生产环境中肯定是不行的。如果要加密，则需要用开启  **Secret 的加密插件** 。

以上当 Pod 变成 Running 状态之后，我们再验证一下这些 Secret 对象是不是已经在容器里了：

```sh
$ kubectl exec -it test-projected-volume -- /bin/sh
$ ls /projected-volume/
user
pass
$ cat /projected-volume/user
root
$ cat /projected-volume/pass
1f2d1e2e67df
```

#### Secret 加密插件



## K8s 附属功能

### Helm

Kubernetes 的包管理工具，类似于 Node.js 通过 npm 来管理包，Debian 系统通过 dpkg 来管理包，而 Python 通过 pip 来管理包。Helm 简化了 Kubernetes 应用的部署和管理，大大提高了效率，越来越多的人在生产环境中使用 Helm 来部署和管理应用。

Helm 有三个核心概念：

- Chart
- Config
- Release



### 日志



## K8S 代码分析



## K8S 常用命令

### Deployment

#### 修改 deployment yaml 文件：kubectl edit

```sh
$ kubectl edit deployment/nginx-deployment
... 
    spec:
      containers:
      - name: nginx
        image: nginx:1.9.1 # 1.7.9 -> 1.9.1
        ports:
        - containerPort: 80
...
deployment.extensions/nginx-deployment edited
```

#### 修改 deployment 中 Pod 的容器镜像版本：kubectl set image xx

```sh
$ kubectl set image deployment/nginx-deployment nginx=nginx:1.91
deployment.extensions/nginx-deployment image updated
```

#### 实时查看 Deployment 的状态变化：kubectl rollout status

```sh
$ kubectl rollout status deployment/nginx-deployment
Waiting for rollout to finish: 2 out of 3 new replicas have been updated...
deployment.apps/nginx-deployment successfully rolled out
```



#### 水平伸缩：kubectl scale xx

```sh
$ kubectl scale deployment nginx-deployment --replicas=4
deployment.apps/nginx-deployment scaled
```

#### 应用回滚 kubectl rollout undo

```sh
$ kubectl rollout undo deployment/nginx-deployment
deployment.extensions/nginx-deployment
```

回滚到指定版本

```sh
$ kubectl rollout undo deployment/nginx-deployment --to-revision=2
deployment.extensions/nginx-deployment
```



### Pod

看一个 Pod 当前的状态：

```sh
$ kubectl get pod twocontainers -o=jsonpath='{.status.phase}'
Pending
```



1、部署 K8S 资源（yml文件）

```
kubectl apply -f xxx.yml
```

2、初始化 Master

```
kubeadm init --apiserver-advertise-address 192.168.56.105 --pod-network-cidr=10.244.0.0/16
```

- `--apiserver-advertise-address` ：指明 Master 的哪个 interface 与 Cluster 中的其他节点通信
- `--pod-network-cidr`：指明 Pod 网络的范围

3、Node 加入指定的 Master 中

```
kubeadm join --token d38a01.13653e584ccc1980 192.168.56.105:6443
```

4、查看机器 节点状态

```
kubectl get nodes
```

5、查看集群所有 Pod 的状态

```
kubectl get pod --all-namespaces -o wide
```

6、查看 Pod 的具体情况

```
kubectl describe pod <pod_name>
```

7、查看 deployment 资源

```
kubectl get deployment
```

8、部署 deployment 资源，并指定副本数

```
kubectl run httpd-app --image=httpd --replicas=2
```

9、删除资源（可以直接按名字删除，也可以删除 yml 文件）

```
kubectl delete -f nginx.yml
kubectl delete deployment xxx_name
```

10、编辑某个服务 xx 的某个资源 yy yaml 文件：

```
kubectl edit xx(daemonset) yy(kube-proxy) --namespace=kube-system
```



Label 定义了其他资源对象所属的标签，类似于你在公司被分到 A 小组、B 小组。有了标签，就可以针对性地对每个小组进行管理。比如把某个小组搬到哪个办公区（把某个 Pod 部署到哪个 Node 上）。给指定的资源对象定义一个或多个不同的标签能够实现多维度的资源分组管理，方便进行资源分配、调度、配置、部署等管理工作。



---

**参考**

《每天5分钟玩转Kubernetes》

https://edu.aliyun.com/course/1651/lesson/list

https://www.cnblogs.com/goldsunshine/p/10701242.html

CKA认证：

https://jimmysong.io/kubernetes-handbook/appendix/about-cka-candidate.html

https://zhuanlan.zhihu.com/p/36835058

Asible 自动化部署 K8S：

https://github.com/easzlab/kubeasz

K8S详细教程：http://www.xfanyi.top/2018/12/05/Kubernetes/

K8S 底层原理剖析：https://www.cnblogs.com/JoZSM/p/10893460.html