# Kubernetes 集群初始化与节点加入指南（修正版）

## ✅ 2.2.6 集群初始化

> **说明**：以下步骤用于初始化 Kubernetes 集群，并将工作节点（node）加入到集群中。**请注意区分 master 节点和 node 节点的操作位置。**

---

## 🟢 一、在 **master 节点** 上执行以下操作：

### 1. 创建集群（初始化 Master）

```bash
[root@master ~]# kubeadm init \
    --kubernetes-version=v1.17.4 \
    --pod-network-cidr=10.244.0.0/16 \
    --service-cidr=10.96.0.0/12 \
    --apiserver-advertise-address=192.168.109.100
```

📌 **参数说明**：
- \`--kubernetes-version\`：指定 Kubernetes 版本（此处为 v1.17.4，建议根据实际环境调整）。
- \`--pod-network-cidr\`：Pod 网络 CIDR，Flannel 等网络插件常用 \`10.244.0.0/16\`。
- \`--service-cidr\`：Service 网络 CIDR，默认 \`10.96.0.0/12\`。
- \`--apiserver-advertise-address\`：API Server 对外广播的 IP 地址（必须是 master 节点的可访问 IP）。

✅ **执行成功后**，会输出类似以下内容（请务必记录）：

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [pod-network].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.109.100:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

---

### 2. 创建必要文件（配置 kubectl 权限）

```bash
[root@master ~]# mkdir -p $HOME/.kube
[root@master ~]# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
[root@master ~]# sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

✅ 此步骤确保当前用户可以使用 \`kubectl\` 命令管理集群。

---

## 🟡 二、在 **node 节点** 上执行以下操作：

### 1. 将 node 节点加入集群

> ⚠️ 注意：\`kubeadm join\` 命令中的 token 和 hash 是 **从 master 初始化输出中复制的**，**不能直接照抄图中内容**（图中 token 和 hash 不完整或被截断）。

正确格式如下（请将'x'替换为实际值）：

```bash
[root@node ~]# kubeadm join 192.168.109.100:6443 \
    --token 8507uc.o0knircuri8etnw2 \
    --discovery-token-ca-cert-hash sha256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

📌 **如何获取正确的 token 和 hash？**

在 master 节点上执行：

```bash
# 查看 token（如果过期需重新生成）
kubeadm token list

# 生成 discovery-token-ca-cert-hash
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der | openssl dgst -sha256 -hex | sed 's/^.* //'
```

然后复制输出的 token 和 hash 到 node 节点的 join 命令中。

---

## ❗ 常见错误与注意事项：

1. **不要在 node 节点上执行 \`kubeadm init\`** —— 会报错。
2. **不要在 master 节点上执行 \`kubeadm join\`** —— 无意义且可能失败。
3. **token 有有效期（默认 24 小时）**，过期后需在 master 上重新生成：
   ```bash
   kubeadm token create --print-join-command
   ```
4. **网络插件未安装会导致节点状态为 NotReady**，需在 master 上安装（如 Flannel、Calico）：
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
   ```

---

## ✅ 最终验证：

在 master 节点上执行：

```bash
kubectl get nodes
```

应看到类似：

```
NAME     STATUS   ROLES    AGE   VERSION
master   Ready    master   10m   v1.17.4
node1    Ready    <none>   5m    v1.17.4
```

---

## 📝 总结

| 步骤 | 操作节点 | 命令 |
|------|----------|------|
| 1. 初始化集群 | master | \`kubeadm init ...\` |
| 2. 配置 kubectl | master | \`mkdir -p ~/.kube && cp /etc/kubernetes/admin.conf ~/.kube/config\` |
| 3. 加入节点 | node | \`kubeadm join ...\`（使用 master 输出的 token 和 hash） |

---

✅ **请根据您的实际环境调整 IP、版本、token 等信息**。如需进一步帮助（如安装网络插件、排查节点 NotReady），请提供 \`kubectl get nodes\` 和 \`kubectl describe node <node-name>\` 输出。
