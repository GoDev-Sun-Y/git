# 在 WSL 中部署 Minikube：完整指南

## 核心结论

在 **WSL** 中部署 **Minikube**，推荐使用 **Docker 驱动**，并配合 **Docker Desktop for Windows** 来提供容器运行时环境。这是目前最稳定、最推荐的方案。

---

## 一、环境准备与安装

### 1. 基础环境要求

在开始之前，请确保你的环境满足以下条件：

| 项目 | 要求 |
|------|------|
| 操作系统 | Windows 10 2004+ 或 Windows 11 |
| WSL 2 | 已安装并设置为默认版本（`wsl --set-default-version 2`） |
| 系统资源 | 至少 **2 个 CPU**、**2 GB 可用内存**、**20 GB 可用磁盘空间** |
| WSL 2 内核版本 | **≥ 5.10**（通过 `wsl -l -v` 查看），否则可能报平台不匹配错误 |

> ⚠️ **注意**：务必确认 WSL 2 内核版本达标，否则启动 Minikube 时可能会报错。

### 2. 安装 Docker

**强烈建议**不要在 WSL 内直接安装 Docker，而是安装 **Docker Desktop for Windows**，并开启 **WSL 2 后端**。

#### 为什么推荐这样做？
- Docker Desktop 会自动配置 WSL 2 作为容器运行环境
- 它会创建一个专用的 WSL 2 实例来托管 Docker 引擎
- WSL 内的 Linux 发行版可以直接使用 Docker 命令

#### 安装步骤

1. 下载并安装 **Docker Desktop for Windows**
2. 在安装向导中勾选 **"Use WSL 2 instead of Hyper-V"** 选项
3. 安装完成后，进入 Docker Desktop 设置
4. 导航到 **Resources → WSL Integration**
5. 确保你使用的 WSL 发行版（如 Ubuntu）的开关是 **打开** 的

### 3. 安装 Minikube

进入你的 WSL 终端（如 Ubuntu），执行以下命令：


推荐使用阿里云镜像加速下载
```bash
curl -LO https://kubernetes.oss-cn-hangzhou.aliyuncs.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

rm minikube-linux-amd64
```
安装完成后，运行以下命令验证：
`minikube version`

### 4. 安装 kubectl

**建议独立安装 kubectl**，而非使用 `minikube kubectl` 命令，因为独立安装的 kubectl 直连 API Server，性能更好。

获取最新稳定版本并安装
```bash
curl -LO "https://dl.k8s.io/release/$(curl
-L -s https://dl.k8s.io/release/stable.txt
)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/
```
> 当前最新稳定版本为：**v1.36.2**

验证安装：
`kubectl version --client`

---

## 二、启动 Minikube 集群

### 1. 配置国内镜像加速

由于网络原因，国内用户启动 Minikube 时**强烈建议**配置阿里云镜像，否则会卡在拉取镜像的步骤上。

执行以下命令启动集群：
```bash
minikube start \

--driver=docker \

--image-mirror-country='cn' \

--registry-mirror=https://2wk9cvvl.mirror.aliyuncs.com
\

--insecure-registry harbor23.
```

不行的话
```bash
# 改域名解析增加一个或多个备用选项

sudo nano /etc/resolv.conf
# 进文件，加一行⬇
nameserver 114.114.114.114

# 拉国内镜像
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kicbase:v0.0.50

# 改名
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kicbase:v0.0.50 gcr.io/k8s-minikube/kicbase:v0.0.50

# 指定用docker驱动 启动集群
minikube start --driver=docker
```

**参数说明：**

| 参数 | 作用 |
|------|------|
| `--driver=docker` | 指定使用 Docker 驱动 |
| `--image-mirror-country='cn'` | 指定使用中国区镜像 |
| `--registry-mirror` | 配置 Docker Hub 镜像加速器 |

### 2. 验证集群状态

启动完成后，通过以下命令验证集群是否正常运行：

查看集群状态
`minikube status`

查看节点信息
`kubectl get nodes`

查看当前上下文，确保连接的是本地集群
`kubectl config current-context`

如果 `kubectl get nodes` 返回节点信息，且状态为 **Ready**，则说明部署成功。

---

## 三、常见问题与避坑指南

### 1. 启动卡住或报错

**现象**：`minikube start` 卡在 `kubelet.resolv-conf` 或报错 `Exiting due to DRV_NOT_DETECTED`

**解决方案**：
- 先执行 `minikube delete` 清理环境
- 确保 Docker Desktop 已正确安装并启动
- 检查 WSL 2 的 DNS 配置，必要时手动设置 `/etc/resolv.conf` 文件，使用 Windows 主机的 WSL 虚拟网卡 IP 作为 DNS 网关

### 2. kubectl 无法连接集群

**现象**：`kubectl get pods` 报错 `The connection to the server was refused`

**解决方案**：
- 确保 Minikube 集群已启动（`minikube status`）
- 使用 `kubectl config current-context` 确认当前上下文是否正确指向 Minikube 集群

### 3. 资源不足导致 Pod 启动失败

**现象**：Pod 一直处于 **Pending** 状态

**解决方案**：
- 检查节点资源：`kubectl describe node`
- 如果资源紧张，尝试停止其他占用资源的程序
- 增加 WSL 的内存分配

### 4. 关于 WSL 和 Minikube 的兼容性

> ⚠️ **重要提醒**：安装在 Windows 上的 Minikube 和 kubectl **无法**直接在 WSL 中使用，因为它们属于不同的环境。务必在 **WSL 内部**安装。

**WSL 的优势**：
- 提供真正的虚拟化 Linux 环境
- 性能优于传统虚拟机
- 能与 Windows 文件系统无缝交互
- 对于开发和学习 Kubernetes 来说，成本最低

---

## 四、总结

通过 **Docker Desktop for Windows + WSL 2** 的组合，你可以在 Windows 上以极低的成本获得一个功能完整的本地 Kubernetes 开发环境。这套方案是目前社区中最主流、最稳定且文档最丰富的选择。