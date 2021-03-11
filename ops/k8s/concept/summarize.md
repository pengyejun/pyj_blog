## 1.为什么需要使用容器？

**<font size=5>众所周知：环境问题是最复杂的问题🙃</font>**

![docker](../../../img/docker.png)
- 传统部署：传统的应用部署方式是通过插件或脚本来安装应用。这样做的缺点显而易见，所有服务都与当前操作系统绑定，服务上线对开发与部署人员都十分痛苦，因此大部分应用都选择用虚拟机发布，但不利于服务移植与管理，并且资源利用率低。

- 容器部署：每个容器之间互相隔离，每个容器有自己的文件系统，容器之间进程不会互相影响，能区分计算资源。相对于虚拟机，容器能快速部署，由于容器与底层设施、宿主机的文件系统是解耦的，所以它能在不同云环境、不同操作系统之间迁移。

**容器的优势总结:**
- 快速创建/部署应用：与虚拟机相比，容器镜像的创建更加容易。
- 持续开发、集成和部署：提供可靠且频繁的容器镜像构建/部署，回滚快速简单(由于镜像不可变性)
- 开发与运行环境分离：在build或者release阶段创建容器镜像，使得应用和基础设施解耦
- 开发，测试和生产环境一致性：在本地或生产环境运行一致
- 云平台或其他操作系统：可以在各种执行docker的环境下运行。
- 弹性，微服务化：应用程序分成更小的、独立的部件，可以动态部署与管理
- 资源隔离与高效利用


## 2.Kubernetes是什么？
Kubernetes是容器集群管理系统，是一个开源的平台，可以实现容器集群的自动化部署、自动扩缩容、维护等功能。
  
### 2.1 Kubernetes 特点
- 可移植：支持公有云，私有云，混合云，多重云
- 可扩展：模块化，插件化，可挂载，可组合
- 自动化：自动部署，自动重启，自动复制，自动扩缩

### 2.2 使用Kubernetes能做什么？

可以在物理或虚拟机的Kubernetes集群上运行容器化应用，Kubernetes能提供一个以`容器为中心的基础架构`，满足在生产环境中运行应用的一些常见需求，如：
- [多个进程（作为容器运行）协同工作。（Pod）]()
- 存储系统挂载
- Distributing secrets？
- 应用健康检测
- [应用实例的复制]()
- Pod自动伸缩/扩展
- 服务发现？
- 负载均衡
- 滚动更新
- 资源监控
- 日志访问
- 调试应用程序
- [提供认证和授权]()

### 2.3 Kubernetes不是什么？
Kubernetes并不是传统的[PaaS（平台即服务）](http://www.ruanyifeng.com/blog/2017/07/iaas-paas-saas.html)系统。

- Kubernetes不限制支持应用的类型，不限制应用框架。不限制受支持的语言，只要应用能在容器里运行，那么就能很好的运行在Kubernetes上。
- Kubernetes不提供中间件、数据库等服务，但这些应用都可以运行在Kubernetes上。

由于Kubernetes运行在应用级别而不是硬件级，因此提供了普通的Paas平台提供的一些通用功能，比如部署，扩展，负载均衡，日志，监控等。这些默认功能是可选的。

另外，Kubernetes不仅仅是一个“编排系统”；它消除了编排的需要。“编排”的定义是指执行一个预定的工作流：先执行A，之B，然C。相反，Kubernetes由一组独立的可组合控制进程组成。怎么样从A到C并不重要，达到目的就好。当然集中控制也是必不可少，方法更像排舞的过程。这使得系统更加易用、强大、弹性和可扩展。