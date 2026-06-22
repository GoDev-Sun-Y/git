# Docker 数据卷与 MySQL 部署笔记

## 数据卷概念

将在宿主机中的目录挂载于容器上，此时宿主机上的目录被称为数据卷。

- **宿主机**：存放实际数据的物理机或虚拟机。
- **容器**：运行应用的隔离环境。
- **挂载**：将宿主机目录映射到容器内目录的过程。
- **数据卷目录**：被挂载的宿主机目录，如 `A.B.C`。

> 图示说明：
> ```
> 宿主机          容器            容器
>  A.B.C  ──挂载──>  A.B.C  (数据卷目录)
> ```

---

## 配置数据卷

使用 `-v` 参数挂载宿主机目录到容器内目录：
```bash
docker run -it ... -v 宿主机目录:容器内目录 ...
```
**示例：**
```bash
docker run -it -v /root/data:/root/data_container ...
```
### 数据卷特性

1. 删除容器后数据仍可恢复（因数据存储在宿主机）。
2. 一个容器可挂载多个数据卷。
3. 多个容器可共享挂载同一个数据卷。

---

## 数据卷容器

### 配置数据卷容器

创建一个专门用于提供数据卷的容器：
```bash
docker run -it --name=c3 -v /volume centos:7 /bin/bash
```
### 配置继承数据卷容器

其他容器可通过 `--volumes-from` 继承已有数据卷容器的挂载：
```bash
docker run -it --name=c1 --volumes-from c3 centos:7 /bin/bash
```
---

## 部署 MySQL

### 步骤 1：拉取 MySQL 镜像
```bash
docker pull mysql:5.6
```
> （安装 MySQL）

### 步骤 2：创建数据目录并进入
```bash
mkdir ~/mysql

cd ~/mysql
```
> （创建目录用于存储数据信息）
- **启动命令**：
 ``` bash
  docker run -id \
    -p 3306:3306 \
    --name=c_mysql \
    -v $PWD/conf:/etc/mysql/conf.d \
    -v $PWD/logs:/logs \
    -v $PWD/data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=123 \
    mysql:5.6
```
## 部署 Tomcat

1. **拉取镜像**：
   bash
   docker pull tomcat
   
2. **创建目录并进入**：
   bash
   mkdir ~/tomcat
   cd ~/tomcat
   
3. **启动容器**：
   ```bash
   docker run -id --name=c_tomcat \
     -p 8080:8080 \
     -v $PWD:/usr/local/tomcat/webapps \
     tomcat
     ```
   

## 部署 Nginx

1. **拉取镜像**：
   ```bash
   docker pull nginx
   ```
2. **创建目录结构并进入配置目录**：
  ``` bash
   mkdir ~/nginx
   cd ~/nginx
   mkdir conf
   cd conf
   ```
3. 编辑配置文件  
   `vim nginx.conf`

4. 启动容器
```bash
docker run -id \
--name=c-nginx \
-p 80:80 \
-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
-v $PWD/logs:/var/log/nginx \
-v $PWD/html:/usr/share/nginx/html \
nginx
```
---

## Redis 部署

1. 拉取镜像  
`docker pull redis:5.0`

2. 启动容器  
`docker run -id --name=c-redis -p 6379:6379 redis:5.0`

3. 客户端连接（可能出问题）  
`./redis-cli.exe -h 192.168.149.135 -p 6379`

---

## Docker 镜像原理

Docker 镜像本质是一个分层文件系统：

- **容器层**（可写容器）
- **只读镜像层**（包含 tomcat、jdk、rootfs 等）
- **宿主机基础镜像**（bootfs，如 centos/ubuntu）

> 镜像分层结构从上到下依次为：应用层 → 运行时依赖层 → 基础系统层 → 宿主机内核层