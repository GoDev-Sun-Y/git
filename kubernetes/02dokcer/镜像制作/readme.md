# Docker 镜像制作与 Dockerfile 案例笔记

## 一、容器转为镜像流程

### 1. 流程图解
- **新镜像** → **压缩文件** → **另一个镜像**
- **可写容器** ↔ **只读容器**
- 底层依赖：`bootfs`

### 2. 操作命令
1. `docker commit 容器id 镜像名称:版本号`  
   （将当前容器提交为新镜像）
2. `docker save -o 压缩文件名称.tar 镜像名称:版本号`  
   （将镜像导出为 tar 压缩包）
3. `docker load -i 压缩文件名称.tar`  
   （从压缩包加载镜像）

---

## 二、Dockerfile 案例：自定义 CentOS7 镜像

### 需求说明
自定义一个 CentOS7 镜像，满足以下要求：
1. 默认登录路径为 `/usr`
2. 可以使用 `vim` 编辑器

### 操作步骤

#### 1. 创建 Dockerfile 目录与文件
```bash

cd ~                    # 切换到 root 用户主目录（需 root 权限）

mkdir docker-files      # 创建构建目录

cd docker-files         # 进入构建目录

vim centos_dockerfile   # 创建并编辑 Dockerfile
```
#### 2. Dockerfile 内容
dockerfile
```bash
#定义父镜像  
FROM centos:7

#定义作者信息  
MAINTAINER  
itheima <itheima@itcast.cn>

#执行安装 vim 命令  
RUN yum install -y vim

#定义默认的工作目录（即默认登录路径）  
WORKDIR /usr

#定义容器启动执行的命令
CMD /bin/bash

#退出vim
```
> **说明**：  
> - `FROM` 指定基础镜像为官方 CentOS 7  
> - `MAINTAINER` 设置镜像维护者信息（旧语法，推荐使用 `LABEL maintainer=`）  
> - `RUN` 在构建时执行命令，安装 vim  
> - `WORKDIR` 设置容器启动时默认工作目录为 `/usr`，满足“默认登录路径为 /usr”的需求

## 命令拆解
```bash
docker build -f ./centos_dockerfile -t itheima-centos:1 .
```
## 参数详解

- `docker build`
  - 构建镜像的命令。
- `-f ./centos_dockerfile`
  - `--file` 的缩写，意为“指定文件路径”。
  - `./`：当前目录。
  - `centos_dockerfile`：文件名。
- `-t itheima-centos:1`
  - `--tag` 的缩写，意为“给镜像起名字”。
  - `itheima-centos`：镜像完整名称。
  - `1`：版本号（标签）。
- `.`（命令末尾）
  - 将当前目录下所有文件发送给 Docker 守护进程，作为构建上下文。

## 命令整体含义

> 以当前的 `centos_dockerfile` 作为说明书，执行其中的 Dockerfile 命令，打包成一个叫 `itheima-centos`、版本是 `1` 的镜像。

# 查看构建结果

- 运行以下命令查看已构建好的镜像：
```bash
docker images
```
# Docker 服务编排

## 服务编排：按照一定的业务规则批量管理容器

### docker compose：

是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动和停止。