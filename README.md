# 背景

​	云场景中基础软件/业务应用之间的边界逐渐上移，基础软件逐渐成为云场景最重要的组成部分，而操作系统又最重要的基础软件之一。
​	从业界公开的数据看，云场景的一些重要故障均是与基础软件密切相关。公开数据显示现有主流云厂商月平均故障150+次数，75%的故障<1H，90%<1.5H，少量故障>5H。

云场景的基础设施、业务场景的复杂性，导致这些故障现象大量集中基础软件（尤其是操作系统）层面。

![](./background.png)

# 介绍

​	针对云场景的故障特点，根据故障发展阶段划分成：系统隐患、灰度故障、故障 三个阶段[1]，openEuler为此针对性的推出AOps解决方案，该解决方案包括多个关键组件，本文用于介绍其中的gala-ops系列组件。

​	gala-ops系列组件定位：云基础设施场景中，针对基础设施**灰度故障**导致**的应用性能劣化、卡**顿系统级故障**在线诊断**。

[1] https://blog.acolyer.org/2017/06/15/gray-failure-the-achilles-heel-of-cloud-scale-systems/

# 原理

通过eBPF技术实现系统白盒化智能观测，实时在线完成系统架构拓扑化，在此基础完成从基础软硬件至应用现象的根因推导过程，且过程可视化。

三步骤如下：

![](./Principle.png)

# 为什么选择gala-ops

## 如何让用户摆脱运维工具的“七国八制”

## 如何让用户打破IT部门之间“运维部门墙”

## 如何让用户实现监控无盲区

## 如何为用户提供基础设施故障定位能力

## 如何为用户提供生产环境性能热点分析能力



# 快速安装

## 架构

gala-ops具备4个组件（gala-gopher、gala-spider、gala-anteater、gala-inference），可以选择集群部署模式、单机部署模式。

集群模式下，需要部署全部组件；单机模式可以只部署gala-gopher。

下图是推荐的集群部署模式，其中gala-gopher位于生产环境Node内，其他组件则位于云厂商管理面。

![](./csp_arch.png)

## gala-gopher

### 定位

作为gala-ops系统的数据采集器，提供应用粒度low-level的数据采集，包括网络、磁盘I/O、调度、内存、安全等方面的系统指标采集，同时负责应用KPI数据的采集。

### 原理及术语

### 支持的技术

### 安装方式



### 被集成方式

### 扩展探针



## gala-spider

## gala-anteater

## gala-inference



# 场景介绍

## 架构感知

gala-ops将系统观测白盒化，通过定义系统观测实体类型以及类型间的关系，完成水平、垂直方向的拓扑结构。

### 支持的实体类型

主机（host）

   |----------------------------------容器（container）

   |----------------------------------|---------------容器内进程（Process）

   |----------------------------------|---------------容器网络namespace （net_ns）

   |----------------------------------|---------------容器mount namespace （mnt_ns）

   |----------------------------------|---------------容器CPU CGroup （cpu_cgp）

   |----------------------------------|---------------容器内存 CGroup （mem_cgp）

   |----------------------------------进程（Process）

   |----------------------------------|---------------进程内线程（Thread）

   |----------------------------------|---------------进程内TCP链接 （tcp_link）

   |----------------------------------|---------------进程内socket（endpoint）

   |----------------------------------|---------------进程对应的高级语言运行时（runtime）

   |----------------------------------|---------------进程对应应用的性能指标（SLI）

   |----------------------------------域名访问（DNS）

   |----------------------------------|---------------客户端IP（client_ip）

   |----------------------------------|---------------DNS Sever IP（server_ip）

   |----------------------------------Nginx

   |----------------------------------|---------------客户端IP（client_ip）

   |----------------------------------|---------------虚拟IP（virtual_ip）

   |----------------------------------|---------------虚拟端口（virtual_port）

   |----------------------------------|---------------后端服务侧IP（server_ip）

   |----------------------------------|---------------后端服务侧端口（server_port）

   |----------------------------------Haproxy

   |----------------------------------|---------------客户端IP（client_ip）

   |----------------------------------|---------------虚拟IP（virtual_ip）

   |----------------------------------|---------------虚拟端口（virtual_port）

   |----------------------------------|---------------后端服务侧IP（server_ip）

   |----------------------------------|---------------后端服务侧端口（server_port）

   |----------------------------------LVS

   |----------------------------------|---------------客户端IP（client_ip）

   |----------------------------------|---------------本地IP（local_ip）

   |----------------------------------|---------------虚拟IP（virtual_ip）

   |----------------------------------|---------------虚拟端口（virtual_port）

   |----------------------------------|---------------后端服务侧IP（server_ip）

   |----------------------------------|---------------后端服务侧端口（server_port）

   |----------------------------------网卡（nic）

   |----------------------------------|---------------网卡队列（qdisc）

   |----------------------------------磁盘（disk）

   |----------------------------------|---------------磁盘逻辑卷/分区（block）

   |----------------------------------网络（net）

   |----------------------------------文件系统（fs）

   |----------------------------------cpu

   |----------------------------------内存（mem）

   |----------------------------------调度（sched）

上述部分观测实体介绍：

- **NGINX（包括LVS/HAPROXY）**：在分布式应用场景中，通常会引入LoadBalancer，以实现业务流的弹性伸缩。通过提供LoadBalancer中间件的观测信息，可以更好的展示分布式应用的实时业务流拓扑。
- **DNS**：云原生微服务场景，服务之间都以域名访问，DNS流普遍应用于各种不同云原生集群中，对其观测可以更好的展示DNS访问情况。
- **ENDPOINT**：进程内的socket信息，包括UDP（客户端/服务端），TCP listen端口，TCP客户端。
- **SLI**: 进程所属应用的SLI，常见的SLI包括应用性能时延，比如HTTP(S)访问时延、redis访问时延、PG访问时延等。SLI可以根据应用场景自由扩展。
- **RUNTIME**：高级语言（包括Java、golang、python等）存在进程级运行时，这些运行时存在GC、协程等机制影响应用性能。



架构感知就是将上述观测实体以拓扑形式呈现给用户，让用户实时了解系统当前运行状态。下面我们以openGauss主备集群来说明其应用效果。

![](./openGauss.png)

### 水平拓扑

水平拓扑用于展示分布式应用的实时业务流，它与垂直拓扑结合在一起，可以帮助用户更好的查看分布式应用的运行状态。

水平拓扑分三层：

- 应用层：呈现应用实例之间的业务流实时拓扑（由进程级拓扑实时计算得出）
- 进程层：呈现进程实例之间的TCP流实时拓扑
- 主机层：呈现主机之间的TCP/IP实时拓扑（由进程级拓扑实时计算得出）

![](./horizontal_topology.png)

水平拓扑API：？

### 垂直拓扑

垂直拓扑用于展示应用实例所在资源节点的上下文依赖关系，用户可以通过扩展应用实例信息轻松获得应用实例运行上下文信息。

通过选择水平拓扑内的对象（比如上图中应用实例、进程实例或Node实例）可以从不同对象实例视角观察垂直拓扑。

下面从左至右分别给出应用实例、进程实例、Node实例视角的垂直拓扑效果。

![](./vertical_topology.png)

垂直拓扑API：？



## 异常检测

gala-ops具备2种异常检测能力：系统异常（也叫系统隐患）、应用KPI异常。前者覆盖网络、磁盘I/O、调度、内存、文件系统等各类系统异常场景；后者包括常见应用时延KPI异常（包括redis、HTTP(S)、PG等），并且用户可以根据自身场景扩展KPI范围（要求KPI数据符合时序数据规范）。

异常检测结果会标识出具体的观测实体，以及异常原因。用户可以通过kafka topic获取系统实时异常信息。通过例子我们解读下异常结果信息：

## 根因定位



## 全栈热点分析



# 常见问题



## 生产环境采集的数据无法送至管理面？

## 如何新增数据采集范围？

## 如何新增应用场景？

## 支持哪些OS？

## 支持哪些内核版本？

## 全栈热点分析调用栈为什么不能准确显示函数名？

# 常用API介绍



# 客户案例

# 合作厂商