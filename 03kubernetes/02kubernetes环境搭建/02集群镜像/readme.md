# 2.2.5 准备集群镜像

## 查看所需镜像

在安装 Kubernetes 集群之前，需要提前准备好集群所需的镜像。可以通过以下命令查看所需的镜像列表：

```bash
[root@master ~]# kubeadm config images list
```

---

## 下载集群镜像

> ⚠️ **注意**：由于网络原因，无法直接连接 `k8s.gcr.io` 官方镜像仓库。以下提供通过**阿里云镜像仓库**下载并重新打标签的替代方案。

### 脚本说明

该脚本通过阿里云镜像源拉取 Kubernetes 所需镜像，重新打上 `k8s.gcr.io` 标签后，删除临时镜像，确保 `kubeadm` 能正常识别。

### 完整脚本

```bash
#!/bin/bash

# 定义所需镜像列表
images=(
    kube-apiserver:v1.17.4
    kube-controller-manager:v1.17.4
    kube-scheduler:v1.17.4
    kube-proxy:v1.17.4
    pause:3.1
    etcd:3.4.3-0
    coredns:1.6.5
)

# 阿里云镜像仓库地址
REGISTRY="registry.cn-hangzhou.aliyuncs.com/google_containers"

# 循环拉取、打标签、清理
for imageName in ${images[@]}; do
    docker pull ${REGISTRY}/${imageName}
    docker tag ${REGISTRY}/${imageName} k8s.gcr.io/${imageName}
    docker rmi ${REGISTRY}/${imageName}
    
done

```

### 执行方式

```bash
# 赋予执行权限
chmod +x prepare-images.sh

# 执行脚本
./prepare-images.sh
```

---

## 验证镜像是否就绪

执行以下命令，确认所有镜像已正确打上 `k8s.gcr.io` 标签：

```bash
[root@master ~]# docker images | grep k8s.gcr.io
```

**预期输出：**

```
k8s.gcr.io/kube-apiserver            v1.17.4    xxx   xxx
k8s.gcr.io/kube-controller-manager   v1.17.4    xxx   xxx
k8s.gcr.io/kube-scheduler            v1.17.4    xxx   xxx
k8s.gcr.io/kube-proxy                v1.17.4    xxx   xxx
k8s.gcr.io/pause                     3.1        xxx   xxx
k8s.gcr.io/etcd                      3.4.3-0    xxx   xxx
k8s.gcr.io/coredns                   1.6.5      xxx   xxx
```

---

## 镜像清单一览

| 序号 | 镜像名称 | 版本 | 用途 |
|:---:|---|---|---|
| 1 | kube-apiserver | v1.17.4 | API 服务器，集群核心组件 |
| 2 | kube-controller-manager | v1.17.4 | 控制器管理器 |
| 3 | kube-scheduler | v1.17.4 | 调度器 |
| 4 | kube-proxy | v1.17.4 | 服务代理 |
| 5 | pause | 3.1 | Pod 基础镜像 |
| 6 | etcd | 3.4.3-0 | 分布式键值存储 |
| 7 | coredns | 1.6.5 | DNS 服务 |

---

## ⚠️ 注意事项

1. **版本匹配**：镜像版本需与 `kubeadm` 版本一致，可通过 `kubeadm config images list` 确认实际所需版本。
2. **网络问题**：若阿里云镜像源也不可达，可尝试其他国内镜像源（如华为云、腾讯云等）。
3. **多节点同步**：如果是多节点集群，需要在 **所有节点**（master 和 worker）上都执行镜像准备操作。
4. **Docker 版本**：确保 Docker 已正确安装并运行（`systemctl status docker`）。
5. **Kubernetes 版本**：本脚本适用于 **v1.17.4**，其他版本请相应调整镜像版本号。

---

## 💡 替代方案：使用 `kubeadm` 直接拉取

如果网络条件允许（已配置代理或镜像加速器），也可直接使用以下命令：

```bash
kubeadm config images pull --image-repository registry.cn-hangzhou.aliyuncs.com/google_containers
```

该命令会自动拉取所有所需镜像并打上正确标签，更加简便。
