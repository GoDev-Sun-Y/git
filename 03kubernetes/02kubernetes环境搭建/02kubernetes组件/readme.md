# 2.2.4 安装 Kubernetes 组件

## 1. 切换国内镜像源（解决国外源速度慢问题）

编辑 `/etc/yum.repos.d/kubernetes.repo`，添加阿里云 Kubernetes 的 YUM 源配置：

```ini
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
        http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
```

---

## 2. 安装 Kubernetes 核心组件

使用 `yum` 安装指定版本（`1.17.4-0`）的 `kubeadm`、`kubelet`、`kubectl`：

```bash
[root@master ~]# yum install --setopt=obsoletes=0 kubeadm-1.17.4-0 kubelet-1.17.4-0 kubectl-1.17.4-0 -y
```

---

## 3. 配置 kubelet 的 cgroup（适配系统驱动）

编辑 `/etc/sysconfig/kubelet`，添加以下配置（指定 cgroup 驱动为 `systemd`，并设置 kube-proxy 模式为 `ipvs`）：

```bash
KUBELET_CGROUP_ARGS="--cgroup-driver=systemd"
KUBE_PROXY_MODE="ipvs"
```

---

## 4. 设置 kubelet 开机自启

通过 `systemctl` 让 `kubelet` 开机自动启动：

```bash
[root@master ~]# systemctl enable kubelet
```

---

## 说明

| 要点 | 说明 |
|------|------|
| 镜像源 | 使用 **阿里云**，避免国外源下载慢的问题 |
| 组件版本 | 安装版本为 `1.17.4-0`，可根据需求调整版本 |
| cgroup 驱动 | `kubelet` 的 cgroup 配置需与容器运行时（如 Docker、containerd）保持一致，这里使用 `systemd` |
| kube-proxy 模式 | 设为 `ipvs` 可提升网络转发性能 |

> ⚠️ **注意**：以上操作需在 Kubernetes 集群的 **Master 节点** 或 **Worker 节点** 执行，需确保系统满足 Kubernetes 安装的前置条件，如关闭 SELinux、配置内核参数等。
