# Docker 私有仓库

## 搭建私有仓库

### 1、拉取私有仓库镜像
docker pull registry

### 2、启动私有仓库容器
docker run -id --name=registry -p 5000:5000 registry

### 3、打开服务器浏览器 输入地址 http://私有仓库服务器ip:5000/v2/_catalog，看到{"repositories":[]}表示私有仓库搭建成功。

### 4、修改 daemon.json
vim /etc/docker/daemon.json

并在上述文件中添加一个 key，保存退出。此步用于让 docker 信任私有仓库地址。注意将私有仓库服务器 IP 修改为自己私有仓库服务器真实 IP。


{"insecure-registries":["私有仓库服务器ip:5000"]}
### 5、重启docker服务
- `systemctl restart docker`
- `docker start registry`

# 将镜像上传至私有仓库

## 1. 标记镜像为私有仓库的镜像
- `docker tag centos:7 私有仓库服务器IP:5000/centos:7`

## 2. 上传标记的镜像(要保持容器 registry 开启)
- `docker push 私有仓库服务器IP:5000/centos:7`

# 从私有仓库拉取镜像
- `docker pull 名字`

# Docker 容器虚拟化与传统虚拟机的比较

## 相同：
- 容器和虚拟机具有相似的资源隔离和分配优势。

## 不同：
- 容器虚拟化的是操作系统，虚拟机虚拟化的是硬件。
- 传统虚拟机可以运行不同的操作系统，容器只能运行同一类型操作系统。