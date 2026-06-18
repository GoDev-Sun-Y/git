# Docker 学习笔记（整理版）

## 一、初识 Docker

### 1.1 代码相关环境

| 编号 | 环境 |
|:---:|:---|
| ① | 开发环境 |
| ② | 测试环境 |
| ③ | 生产环境 |

### 1.2 代码交付流程

```
开发环境  ──(.war 包)──▶  测试环境  ──(测试通过后)──▶  生产环境
```

> **流程说明**：代码在 **开发环境** 完成后，以 `.war` 包的形式交付给 **② 测试环境**；测试通过后，`.war` 包再交付给 **③ 生产环境**。

### 1.3 容器的作用

为解决 **软件跨环境迁移时的环境不匹配问题**，将 **应用代码 + 运行环境** 一同打包的技术称为 **容器（Container）**。

---

## 二、Docker 概念

**Docker** 是一个开源的应用容器引擎。

---

## 三、安装 Docker（基于 CentOS 系统）

### 步骤 1：更新 yum 包到最新

```bash
yum update
```

### 步骤 2：安装依赖软件包

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```

| 依赖包 | 作用 |
|:---|:---|
| `yum-utils` | 提供 `yum-config-manager` 功能 |
| `device-mapper-persistent-data` | `devicemapper` 存储驱动的依赖 |
| `lvm2` | `devicemapper` 存储驱动的依赖（原笔记遗漏） |

### 步骤 3：设置 Docker 的 yum 源

```bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 步骤 4：安装 Docker 社区版（Docker CE）

```bash
yum install -y docker-ce
```

> 出现交互提示时输入 `y` 确认；使用 `-y` 参数可自动确认。

### 步骤 5：验证安装（查看 Docker 版本）

```bash
docker -v
```

---

## 四、补充说明

- Docker 的核心是 **容器化**，将应用及其依赖打包成标准化单元，实现 **"一次构建，到处运行"**，解决传统部署中"开发、测试、生产环境不一致"的问题。
- 以上安装步骤基于 **CentOS**，若使用其他 Linux 发行版（如 Ubuntu），安装命令会有差异（需用 `apt` 而非 `yum`）。




# 一、Docker 架构

| 层级 | 组件 | 说明 |
|------|------|------|
| **Clients（客户端）** | `docker` CLI、`docker-machine` | 用户与 Docker 交互的命令行工具 |
| **Hosts（Docker 主机/服务端）** | `Docker Daemon`（守护进程）、`Images`（镜像）、`Containers`（容器） | Docker 核心运行环境 |
| **Registries（镜像仓库）** | `Docker Hub`（官方仓库）、`Private Registry`（私有仓库） | 存储和分发镜像的地方 |

---

## 二、配置 Docker 镜像加速器

> Docker 官方仓库：`https://hub.docker.com`

### 常用国内镜像加速器地址

| 服务商 | 镜像加速器地址 |
|--------|---------------|
| **中科大** | `https://docker.mirrors.ustc.edu.cn` |
| **网易云** | `https://hub-mirror.c.163.com` |
| **腾讯云** | `https://mirror.ccs.tencentyun.com` |
| **阿里云** | 需登录阿里云控制台 → 容器镜像服务 → 获取专属加速地址 |

> ⚠️ **注意**：`hub.docker.com` 是 Docker 官方的镜像仓库（Registry），**不是**加速器。加速器是加速从仓库拉取镜像的代理地址。

---

## 三、Docker 命令体系

### 1. 服务/守护进程管理（Systemctl）

| 命令 | 说明 |
|------|------|
| `systemctl start docker` | 启动 Docker 服务 |
| `systemctl stop docker` | 停止 Docker 服务 |
| `systemctl restart docker` | 重启 Docker 服务 |
| `systemctl status docker` | 查看 Docker 服务状态 |
| `systemctl enable docker` | 设置 Docker 开机自启 |
| `docker version` | 查看 Docker 版本信息 |
| `docker info` | 查看 Docker 系统详细信息 |

### 2. 镜像操作（Image）

| 命令 | 说明 |
|------|------|
| `docker pull <镜像名>` | 拉取镜像 |
| `docker images` / `docker image ls` | 查看本地镜像列表 |
| `docker rmi <镜像名>` / `docker image rm` | 删除镜像 |
| `docker push <镜像名>` | 推送镜像到仓库 |

### 3. 容器操作（Container）

| 命令 | 说明 |
|------|------|
|`docker run -it --name=容器名 镜像:版本 /bin/bash`|创建并启动容器|
| `docker run -d --name <容器名> <镜像名>` | 后台运行容器 |
| `docker start <容器名>` | 启动已停止的容器 |
| `docker stop <容器名>` | 停止运行中的容器 |
| `docker exec -it <容器名> /bin/bash` | 进入运行中的容器 |
| `docker ps` | 查看运行中的容器 |
| `docker ps -a` | 查看所有容器（含已停止的） |
| `docker rm <容器名>` | 删除容器 |

---

> 📌 **小贴士**：Docker 命令在较新版本中推荐使用子命令格式，如 `docker image ls` 替代 `docker images`，`docker container run` 替代 `docker run`，两者等价但前者语义更清晰。
