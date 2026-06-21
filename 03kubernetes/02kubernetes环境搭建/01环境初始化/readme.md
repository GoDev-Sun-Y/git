#  环境初始化

## 1) 检查操作系统的版本

此方式下安装 Kubernetes 集群要求 CentOS 版本要在 7.5 或之上。
```bash

[root@master ~]# cat /etc/redhat-release

CentOS Linux release 7.5.1804 (Core)

````
---

## 2) 主机名解析

为了方便后面集群节点间的直接调用，在这配置一下主机名解析，企业中推荐使用内部 DNS 服务器。
```bash

主机名成解析 编辑三台服务器的 /etc/hosts 文件，添加下面内容

vim /etc/hosts
#添加
192.168.109.100  master
192.168.109.101  node1
192.168.109.102  node2
```
---

## 3) 时间同步

Kubernetes 要求集群中的节点时间必须精确一致，这里直接使用 `chronyd` 服务从网络同步时间。  
企业中建议配置内部的时间同步服务器。
```bash

启动 chronyd 服务
[root@master ~]# systemctl start chronyd

设置 chronyd 服务开机自启
[root@master ~]# systemctl enable chronyd

chronyd 服务启动稍等几秒钟，就可以使用 date 命令验证时间了
[root@master ~]# date

```
---

## 4) 禁用 iptables 和 firewalld 服务

Kubernetes 和 Docker 在运行中会产生大量的 iptables 规则，为了不让系统规则跟它们混淆，直接关闭系统的规则。
```bash

1 关闭 firewalld 服务
[root@master ~]# systemctl stop firewalld

[root@master ~]# systemctl disable firewalld

2 关闭 iptables 服务
[root@master ~]# systemctl stop iptables

[root@master ~]# systemctl disable iptables

```
---

## 5) 禁用 SELinux

SELinux 是 Linux 系统下的一个安全服务，如果不关闭它，在安装集群中会产生各种各样的奇葩问题。  

查看这个服务的状态
```bash
getenforce
```
编辑 /etc/selinux/config 文件，修改 SELINUX 的值为 disabled
注意修改完毕之后需要重启 linux 服务
```bash
vim /etc/selinux/config
#文档中这里更改为
SELINUX=disabled
```
---

## 6) 禁用 swap 分区

Swap 分区指的是虚拟内存分区，它的作用是在物理内存使用完之后，将磁盘空间虚拟成内存来使用。  
启用 swap 设备会对系统的性能产生非常负面的影响，因此 Kubernetes 要求每个节点都要禁用 swap 设备。  
但是如果因为某些原因确实不能关闭 swap 分区，就需要在集群安装过程中通过明确的参数进行配置说明。

编辑分区配置文件 /etc/fstab，注释掉 swap 分区一行
注意修改完毕之后需要重启 linux 服务
```bash
vim /etc/fstab
#注释掉指定内容
```
| UUID=455cc753-7a60-4c17-a424-7741728c44a1 /boot | xfs | defaults | 0 0 |
|------|------|------|------|
| /dev/mapper/centos-home /home | xfs | defaults | 0 0 |
| /dev/mapper/centos-swap swap  | swap| defaults | 0 0 |

## 7) 修改Linux的内核参数

- **目标**：添加网桥过滤和地址转发功能
- **操作**：编辑 `/etc/sysctl.d/kubernetes.conf` 文件，添加如下配置：
```bash
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
```
- **重新加载配置**：
```bash

[root@master ~]# sysctl -p

```
- **加载网桥过滤模块**：
```bash
[root@master ~]# modprobe br_netfilter
```
- **查看网桥过滤模块是否加载成功**：
```bash
[root@master ~]# lsmod | grep br_netfilter
```
---

## 8) 配置IPVS功能

在 Kubernetes 中，Service 有两种代理模型：
- 一种是基于 `iptables` 的
- 一种是基于 `ipvs` 的

两者比较：`ipvs` 的性能明显更高，但使用前需手动载入 `ipvs` 模块。

### 步骤：

1. **安装 ipset 和 ipvsadm**
```bash
[root@master ~]# yum install ipset ipvs
(如果不成功)
# 1. 进入 yum 源目录
cd /etc/yum.repos.d/

# 2. 备份原有 repo
mkdir -p backup && mv *.repo backup/

# 3. 下载阿里云 CentOS 7 源（适配 EOL 后的 vault）
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo

# 4. 清理并重建缓存
yum clean all && yum makecache
# 5. 测试是否成功
yum repolist
# 6. 重新用yum下载
```
2. **添加需要加载的模块写入脚本文件**
```bash
[root@master ~]# cat <<EOF > /etc/sysconfig/modules/ipvs.modules
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
```
3. **为脚本文件添加执行权限**
```bash
[root@master ~]# chmod +x /etc/sysconfig/modules/ipvs.modules
```
4. **执行脚本文件**
```bash
[root@master ~]# /bin/bash /etc/sysconfig/modules/ipvs.modules
```
5. **查看对应的模块是否加载成功**
```bash
[root@master ~]# lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```
---

## 9) 重启服务器

上面步骤完成之后，需要重新启动 Linux 系统：
```bash
[root@master ~]# reboot
```