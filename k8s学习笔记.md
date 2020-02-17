[TOC]

### 0 每周工作计划与工作汇报相关

* k8s分工（02月12日）：
  方向：源码分析、官网文档、paper
  本科生：函数源码阅读

  汇总看到内容，做成PPT，每周meeting
  分解任务，可以让本科生去看

* 周工作汇报PPT内容：上周做了什么？发现了什么问题？有什么问题还需要解决？遇到了什么困难？下周的计划与分工是什么？

#### 0.1 第一周

1、确定看的k8s的代码版本
2、了解k8s是什么？有什么用？有哪些模块与组件，每个模块与组件是做什么的？模块学习顺序是什么？
3、本工作的整体的安排是什么？下周计划与分工什么？
4、k8s的工作流程
第一周工作汇报PPT内容（计划）：
1、确定看的k8s的代码版本
2、了解k8s是什么？有什么用？有哪些模块与组件，每个模块与组件是做什么的？模块学习顺序是什么？（看的时候直接做成PPT，最后汇成1份做成周工作汇报）
3、本工作的整体的安排是什么？下周计划与分工什么？
4、执行流程，就是从接受命令到完成调度的过程什么样之类

#### 0.2 第二周





### 1 K8s概述

#### 1.1 什么是K8s?

Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。K8s提供的服务和功能包括：

* 服务发现和负载均衡
* 存储编排：允许自动挂载所选择的存储系统，例如本地存储、公共云提供商等
* 自动部署和回滚
* 自我修复：k8s会重启失败的容器，替换容器，杀死不响应用户定义的运行状况检查的容器
* 密钥与配置管理

#### 1.2 K8s的基本概念

#### 1.2.1 k8s的组件

**Master节点：**运行k8s Master组件的节点称为Master节点。运行该组件的节点可以是一台物理机也可以是一台虚拟机。Master节点通常用来维护和管理集群状态。k8s master组件通常包含3个进程，包括kube-apiserver、kube-controller、kube-scheduler，而且这些进程通常都跑在集群中一个单独的节点上。Master节点可扩展副本数，获取更好的可用性及冗余。用户通常会与master节点通信。

* kube-apiserver：验证和配置API对象（如pods，services，replicationcontrollers等）的数据。提供REST操作，并为集群共享状态提供前端，而且所有其他组件都通过它进行交互。
* kube-scheduler：监控新创建的未指定运行节点的Pod（调度最小单元）
* kube-controller-manager：所有资源对象的自动化控制中心。简单理解就是Master节点上运行控制器的组件。用来控制Pod的状态和生命周期的。它运行的控制器包括：
  * 节点控制器（Node Controller）: 负责在节点出现故障时进行通知和响应。
  * 副本控制器（Replication Controller）: 负责为系统中的每个副本控制器对象维护正确数量的 Pod。
  * 端点控制器（Endpoints Controller）: 填充端点(Endpoints)对象(即加入 Service 与 Pod)。
  * 服务帐户和令牌控制器（Service Account & Token Controllers）: 为新的命名空间创建默认帐户和 API 访问令牌。
* etcd：兼具一致性和高可用性的KV数据库，可保存k8s所有集群数据

**Node节点**：用来运行具体应用和云工作流的机器（虚拟机、物理机等等），master节点控制所有的Node节点。用户很少需要和Node节点进行直接通信。包含2个进程。

* kubelet：和master节点进行通信，一个在集群中每个节点上运行的代理。它保证容器都运行在 Pod 中。kubelet 接收一组通过各类机制提供给它的 PodSpecs，确保这些 PodSpecs 中描述的容器处于运行状态且健康。kubelet 不会管理不是由 Kubernetes 创建的容器。
* kube-proxy：一种网络代理，将k8s的网络服务代理到每个节点上。
* Container runtime：是负责运行容器的软件，如docker，containerd等

**Pod**：Pod是k8s应用程序的基本执行单元，即它是k8s对象模型中创建或部署的最小和最简单的单元。Pod表示在集群上运行的进程。Pod 封装了应用程序容器（或者在某些情况下封装多个容器）、存储资源、唯一网络 IP 以及控制容器应该如何运行的选项。简单理解就是Pod是一组（一个或多个）容器，这些容器共享网络、存储以及怎样运行这些容器的声明。Pod 中的所有容器共享一个 IP 地址和端口空间，并且可以通过 `localhost` 互相发现。他们也能通过标准的进程间通信（如 SystemV 信号量或 POSIX 共享内存）方式进行互相通信。不同 Pod 中的容器的 IP 地址互不相同，没有 [特殊配置](https://kubernetes.io/docs/concepts/policy/pod-security-policy/) 就不能使用 IPC 进行通信。这些容器之间经常通过 Pod IP 地址进行通信。k8s集群中的Pod的2个主要用途：

* **运行单个容器的Pod：**”每个 Pod 一个容器”模型是最常见的 Kubernetes 用例；在这种情况下，可以将 Pod 看作单个容器的包装器，并且 Kubernetes 直接管理 Pod，而不是容器。
* **运行多个协同工作的容器的Pod：** Pod 可能封装由多个紧密耦合且需要共享资源的共处容器组成的应用程序。 这些位于同一位置的容器可能形成单个内聚的服务单元 —— 一个容器将文件从共享卷提供给公众，而另一个单独的“挂斗”（sidecar）容器则刷新或更新这些文件。 Pod 将这些容器和存储资源打包为一个可管理的实体。

#### 1.2.2  Master节点到集群的通信

从 master（apiserver）到集群有两种主要的通信路径。第一种是从 apiserver 到集群中每个节点上运行的 kubelet 进程。第二种是从 apiserver 通过它的代理功能到任何 node、pod 或者 service。

* apiserver->kubelet：从 apiserver 到 kubelet 的连接用于获取 pods 日志、连接（通过 kubectl）运行中的 pods，以及使用 kubelet 的端口转发功能。这些连接终止于 kubelet 的 HTTPS endpoint。默认的，apiserver 不会验证 kubelet 的服务证书，这会导致连接遭到中间人攻击，因而在不可信或公共网络上是不安全的。

* apiserver->nodes,pods and services：从 apiserver 到 node、pod 或者 service 的连接默认为纯 HTTP 方式，因此既没有认证，也没有加密。他们能够通过给 API URL 中的 node、pod 或 service 名称添加前缀 `https:` 来运行在安全的 HTTPS 连接上。但他们即不会认证 HTTPS endpoint 提供的证书，也不会提供客户端证书。这样虽然连接是加密的，但它不会提供任何完整性保证。这些连接**目前还不能安全的**在不可信的或公共的网络上运行。

  参考链接：[Master节点通信](https://kubernetes.io/zh/docs/concepts/architecture/master-node-communication/)

#### 1.3 资源对象与基本概念解析

##### 1.3.1 Pod

1. Pod的生命周期：Pod 的 `status` 字段是一个 PodStatus 对象，PodStatus中有一个 `phase` 字段。Pod 的`phase`是 Pod 在其生命周期中的简单宏观概述。`phase`可能的值如下：

   * 挂起（Pending）：Pod 已被 Kubernetes 系统接受，但有一个或者多个容器镜像尚未创建。等待时间包括调度 Pod 的时间和通过网络下载镜像的时间，这可能需要花点时间。
   * 运行中（Running）：该 Pod 已经绑定到了一个节点上，Pod 中所有的容器都已被创建。至少有一个容器正在运行，或者正处于启动或重启状态。
   * 成功（Succeeded）：Pod 中的所有容器都被成功终止，并且不会再重启。
   * 失败（Failed）：Pod 中的所有容器都已终止了，并且至少有一个容器是因为失败终止。也就是说，容器以非0状态退出或者被系统终止。
   * 未知（Unknown）：因为某些原因无法取得 Pod 的状态，通常是因为与 Pod 所在主机通信失败。

   下图为Pod的生命周期示意图：

   ![Pod的生命周期示意图](https://jimmysong.io/kubernetes-handbook/images/kubernetes-pod-life-cycle.jpg)

##### 1.3.2 集群资源管理

1. Node
   * Node是k8s集群的工作节点，可以是物理机也可以是虚拟机。
   * Node包括如下状态信息：
     * Address
       * HostName：主机名称，可以被kubulet中的--hostname-override替代。
       * ExternalIP：可以被集群外部路由到的IP地址
       * InternalIP：集群内部使用的IP
     * Condition
       * OutOfDIsk：磁盘空间不足时为True。
       * Ready：Node Controller 40s没有收到Node的报告时为Unknown，健康为True，否则为False。
       * MemoryPressure：当前Node是否有内存压力。
       * DiskPressure：当前Node是否有磁盘压力。
     * Capacity
       * CPU
       * 内存
       * 可运行的最大Pod个数。
     * Info：节点的一些版本信息，如OS、k8s、docker等。

2. Namespace
   * 在一个Kubernetes集群中可以使用namespace创建多个“虚拟集群”，这些namespace之间可以完全隔离，也可以通过某种方式，让一个namespace中的service可以访问到其他的namespace中的服务。
   * 因为namespace可以提供独立的命名空间，因此可以实现部分的环境隔离。当你的项目和人员众多的时候可以考虑根据项目属性，例如生产、测试、开发划分不同的namespace。
   * 集群中默认会有`default`和`kube-system`这两个namespace。
   * 用户的普通应用默认是在`default`下，与集群管理相关的为整个集群提供服务的应用一般部署在`kube-system`的namespace下，例如我们在安装k8s集群时部署的`kubedns`、`heapseter`、`EFK`等都是在这个namespace下面。
   * 另外，并不是所有的资源对象都会对应namespace，`node`和`persistentVolume`就不属于任何namespace。

3. Label
   * Label是附着在对象（如Pod等）上的键值对。可以在创建对象的时候指定，也可以在对象创建后随时指定。Labels的值对系统本身并没有什么含义，只是对用户才有意义。
   * k8s最终将对labels最终索引和反向索引用来优化查询和watch，在UI和命令行中会对它们排序。不要在label中使用大型、非标识的结构化数据，记录这样的数据应该用annotation。
   * Label不是唯一的，很多object可能有相同的label。通过label selector，客户端／用户可以指定一个object集合，通过label selector对object的集合进行操作。
   * Label Selector有两种类型：
     * *equality-based* ：可以使用`=`、`==`、`!=`操作符，可以使用逗号分隔多个表达式。
     * *set-based* ：可以使用`in`、`notin`、`!`操作符，另外还可以没有操作符，直接写出某个label的key，表示过滤有某个key的object而不管该key的value是何值，`!`表示没有该label的object。

4. Taint和Toleration
   * Taint（污点）和 Toleration可以作用于 node 和 pod 上，其目的是优化 pod 在集群间的调度，这跟节点亲和性类似，只不过它们作用的方式相反，具有 taint 的 node 和 pod 是互斥关系，而具有节点亲和性关系的 node 和 pod 是相吸的。另外还有可以给 node 节点设置 label，通过给 pod 设置 `nodeSelector` 将 pod 调度到具有匹配标签的节点上。
   * Taint 和 toleration 相互配合，可以用来避免 pod 被分配到不合适的节点上。每个节点上都可以应用**一个或多个** taint ，这表示对于那些不能容忍这些 taint 的 pod，是不会被该节点接受的。如果将 toleration 应用于 pod 上，则表示这些 pod 可以（但不要求）被调度到具有相应 taint 的节点上。

5. 垃圾收集
   * k8s垃圾收集器的作用是删除某些曾经拥有所有者（owner）但现在不再拥有所有者的对象。
   * 某些 Kubernetes 对象是其它一些对象的所有者。例如，一个 ReplicaSet 是一组 Pod 的所有者。 具有所有者的对象被称为是所有者的*附属*。 每个附属对象具有一个指向其所属对象的 `metadata.ownerReferences` 字段。
   * 当删除对象时，可以指定该对象的附属者是否也自动删除掉。 自动删除 Dependent 也称为 *级联删除* 。 Kubernetes 中有两种 *级联删除* 的模式：*background* 模式和 *foreground* 模式。如果删除对象时，不自动删除它的附属者，这些附属者被称作是原对象的 *orphaned* 。
   * [参考链接](https://kubernetes.io/zh/docs/concepts/workloads/controllers/garbage-collection/)

##### 1.3.3 控制器

1. ReplicationController和ReplicationSet
   * *ReplicationController* 确保在任何时候都有特定数量的 pod 副本处于运行状态。 换句话说，ReplicationController 确保一个 pod 或一组同类的 pod 总是可用的。如果有容器异常退出，会自动创建新的Pod来替代；而如果异常多出来的容器也会自动回收。
   * ReplicaSet 是下一代的 Replication Controller。 *ReplicaSet* 和 [*Replication Controller*](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/) 的唯一区别是选择器的支持。ReplicaSet 支持新的基于集合的选择器需求，这在[标签用户指南](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors)中有描述。而 Replication Controller 仅支持基于相等选择器的需求。

2. Deployment
   * Deployment为Pod和Replica Set（下一代Replication Controller）提供声明式更新.
   * 您只需要在 Deployment 中描述您想要的目标状态是什么，Deployment controller 就会帮您将 Pod 和ReplicaSet 的实际状态改变到您的目标状态。您可以定义一个全新的 Deployment 来创建 ReplicaSet 或者删除已有的 Deployment 并创建一个新的来替换。
   * 不该手动管理由 Deployment 创建的 ReplicaSet，否则就篡越了 Deployment controller 的职责！
   * 典型用例如下：
     * [创建 Deployment 以展开 ReplicaSet ](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)。 ReplicaSet 在后台创建 Pods。检查 ReplicaSet 展开的状态，查看其是否成功。
     * [声明 Pod 的新状态](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) 通过更新 Deployment 的 PodTemplateSpec。将创建新的 ReplicaSet ，并且 Deployment 管理器以受控速率将 Pod 从旧 ReplicaSet 移动到新 ReplicaSet 。每个新的 ReplicaSet 都会更新 Deployment 的修改历史。
     * [回滚到较早的 Deployment 版本](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment)，如果 Deployment 的当前状态不稳定。每次回滚都会更新 Deployment 的修改。
     * [扩展 Deployment 以承担更多负载](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#scaling-a-deployment).
     * [暂停 Deployment](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#pausing-and-resuming-a-deployment) 对其 PodTemplateSpec 进行修改，然后恢复它以启动新的展开。
     * [使用 Deployment 状态](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#deployment-status) 作为卡住展开的指示器。
     * [清理较旧的 ReplicaSets](https://kubernetes.io/zh/docs/concepts/workloads/controllers/deployment/#clean-up-policy) ，那些不在需要的。

3. StatefulSet
   * StatefulSet 是用来管理有状态应用的工作负载 API 对象。StatefulSet 用来管理 Deployment 和扩展一组 Pod，并且能为这些 Pod 提供*序号和唯一性保证*。
   * 和 [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 相同的是，StatefulSet 管理了基于相同容器定义的一组 Pod。但和 Deployment 不同的是，StatefulSet 为它们的每个 Pod 维护了一个固定的 ID。这些 Pod 是基于相同的声明来创建的，但是不能相互替换：无论怎么调度，每个 Pod 都有一个永久不变的 ID。
   * StatefulSet 和其他控制器使用相同的工作模式。你在 StatefulSet *对象* 中定义你期望的状态，然后 StatefulSet 的 *控制器* 就会通过各种更新来达到那种你想要的状态。

4. DaemonSet
   * *DaemonSet* 确保全部（或者某些）节点上运行一个 Pod 的副本。当有节点加入集群时， 也会为他们新增一个 Pod 。当有节点从集群移除时，这些 Pod 也会被回收。删除 DaemonSet 将会删除它创建的所有 Pod。
   * 一个简单的用法是在所有的节点上都启动一个 DaemonSet，将被作为每种类型的 daemon 使用。一个稍微复杂的用法是单独对每种 daemon 类型使用多个 DaemonSet，但具有不同的标志， 并且对不同硬件类型具有不同的内存、CPU 要求。
   * 其典型用法如下：
     * 在每个节点上运行集群存储 DaemonSet，例如 `glusterd`、`ceph`。
     * 在每个节点上运行日志收集 DaemonSet，例如 `fluentd`、`logstash`。
     * 在每个节点上运行监控 DaemonSet，例如 [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)、[Flowmill](https://github.com/Flowmill/flowmill-k8s/)、[Sysdig 代理](https://docs.sysdig.com/)、`collectd`、[Dynatrace OneAgent](https://www.dynatrace.com/technologies/kubernetes-monitoring/)、[AppDynamics 代理](https://docs.appdynamics.com/display/CLOUD/Container+Visibility+with+Kubernetes)、[Datadog 代理](https://docs.datadoghq.com/agent/kubernetes/daemonset_setup/)、[New Relic 代理](https://docs.newrelic.com/docs/integrations/kubernetes-integration/installation/kubernetes-installation-configuration)、Ganglia `gmond` 或者 [Instana 代理](https://www.instana.com/supported-integrations/kubernetes-monitoring/)
5. Job
   * [参考链接](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)
6. CronJob
   * [参考链接](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

##### 1.3.4 服务发现

k8s中为了实现服务实例间的负载均衡和不同服务间的服务发现，创造了Serivce对象，同时又为从集群外部访问集群创建了Ingress对象。

1. Services
   * 动机
     * k8s[Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 是有生命周期的。他们可以被创建，而且销毁不会再启动。 如果您使用 [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 来运行您的应用程序，则它可以动态创建和销毁 Pod。每个 Pod 都有自己的 IP 地址，但是在 Deployment 中，在同一时刻运行的 Pod 集合可能与稍后运行该应用程序的 Pod 集合不同。这会导致一个问题：在 Kubernetes 集群中，如果一组 `Pod`（称为 backend）为其它 `Pod` （称为 frontend）提供服务，那么那些 frontend 该如何发现，并连接到这组 `Pod`中的哪些 backend 呢？
   * k8s`Service` 定义了这样一种抽象：逻辑上的一组 `Pod`，一种可以访问它们的策略 —— 通常称为微服务。 这一组 `Pod` 能够被 `Service` 访问到，通常是通过 [selector](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) （查看[下面](https://kubernetes.io/zh/docs/concepts/services-networking/service/#services-without-selectors)了解，为什么你可能需要没有 selector 的 `Service`）实现的。
   * 如果您想要在应用程序中使用 Kubernetes 接口进行服务发现，则可以查询 [API server](https://kubernetes.io/docs/reference/generated/kube-apiserver/) 的 endpoint 资源，只要服务中的Pod集合发生更改，端点就会更新。对于非本机应用程序，Kubernetes提供了在应用程序和后端Pod之间放置网络端口或负载均衡器的方法。
2. Ingress
   * [参考链接](https://kubernetes.io/zh/docs/concepts/services-networking/ingress/)



### 5. 学习过程中的有用资料

#### 5.1 网页链接

* [k8s github地址](https://github.com/kubernetes/kubernetes)
* [k8s github 版本更新历史](https://github.com/kubernetes/kubernetes/releases)
* [k8s v1.13源码分析](https://daniel-hutao.github.io/k8s-source-code-analysis/)
* [k8s概念与原理](https://jimmysong.io/kubernetes-handbook/concepts/)
* [k8s中文文档](http://docs.kubernetes.org.cn/)



#### 5.2 相关论文





