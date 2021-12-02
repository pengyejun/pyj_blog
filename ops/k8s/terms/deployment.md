## Deployment 概述
Deployment 是最常用的用于部署无状态服务的方式。Deployment 控制器使得您能够以声明的方式更新 Pod（容器组）和 ReplicaSet（副本集）。

声明式配置

声明的方式是相对于非声明方式而言的。例如，以滚动更新为例，假设有 3 个容器组，现需要将他们的容器镜像更新为新的版本。
  - 非声明的方式，您需要手动逐步执行以下过程：
    - 使用 kubectl 创建一个新版本镜像的容器组
    - 使用 kubectl 观察新建容器组的状态
    - 新建容器组的状态就绪以后，使用 kubectl 删除一个旧的容器组
    - 重复执行上述过程，直到所有容器组都已经替换为新版本镜像的容器组
  
声明的方式，您只需要执行：
  - 使用 kubectl 更新 Deployment 定义中 spec.template.spec.containers[].image 字段