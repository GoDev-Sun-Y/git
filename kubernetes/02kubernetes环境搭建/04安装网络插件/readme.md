#  安装网络插件（Flannel）

Kubernetes 支持多种网络插件，例如 Flannel、Calico、Canal 等，**任选其一即可**。本次选择 **Flannel**。

> 📌 **注意**：以下操作**仅在 master 节点执行**。插件通过 DaemonSet 控制器部署，会在每个节点上自动运行。

---

## 🔧 步骤一：获取 Flannel 配置文件

```bash
[root@master ~]# wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```


---

## 🔧 步骤二：修改镜像仓库地址（可选，用于加速）

由于 `quay.io` 在国内访问可能较慢，建议将配置文件中镜像仓库替换为国内镜像源：

```bash
# 替换 quay.io 为 quay-mirror.qiniu.com（七牛云镜像）
sed -i 's|quay.io|quay-mirror.qiniu.com|g' kube-flannel.yml
```

> 💡 原图中仅写“修改文件中quay.io仓库为quay-mirror.qiniu.com”，但未给出具体命令，建议补充 `sed` 命令以便直接执行。

---

## 🔧 步骤三：应用配置文件启动 Flannel

```bash
[root@master ~]# kubectl apply -f kube-flannel.yml
```

---

## 🔧 步骤四：等待片刻，检查节点状态

```bash
[root@master ~]# kubectl get nodes
```

**预期输出示例：**

```
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   15m     v1.17.4
node1    Ready    <none>   8m53s   v1.17.4
node2    Ready    <none>   8m50s   v1.17.4
```

> ✅ 所有节点状态为 `Ready`，表示网络插件安装成功。

---

## 🎉 至此，Kubernetes 集群环境搭建完成！

---

## 📝 补充说明：

- **Flannel 作用**：为集群提供 Pod 网络通信（Overlay 网络），实现跨节点 Pod 互通。
- **DaemonSet 特性**：确保集群中每个节点都运行一个 Flannel Pod。
- **版本兼容性**：请确保 `kube-flannel.yml` 与您的 Kubernetes 版本兼容（如 v1.17.4）。
- **镜像加速**：若不使用镜像替换，也可配置 Docker 镜像加速器或使用代理。

---

## ✅ 最终整理版（可直接复制使用）：

```bash
# 2.2.7 安装网络插件（Flannel）

# 1. 获取 Flannel 配置文件
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 2. （可选）替换镜像仓库为国内加速源
sed -i 's|quay.io|quay-mirror.qiniu.com|g' kube-flannel.yml

# 3.假设本地镜像为 flannel:v0.12.0-amd64（请替换为你实际的镜像名）
sed -i 's|quay.io/coreos/flannel:v0.12.0-amd64|flannel:v0.12.0-amd64|g' kube-flannel.yml

# 4. 应用配置
kubectl apply -f kube-flannel.yml

# 5. 等待片刻，查看节点状态
kubectl get nodes

# 如果发现问题，检查/etc/cni/net.d/
ls /etc/cni/net.d/
# 如果为空或无
mkdir -p /etc/cni/net.d/
#
cat > /etc/cni/net.d/10-flannel.conflist <<EOF

{

"name": "cbr0",

"cniVersion": "0.3.1",

"plugins": [

{

"type": "flannel",

"delegate": {

"hairpinMode": true,

"isDefaultGateway": true

}

},

{

"type": "portmap",

"capabilities": {

"portMappings": true

}

}

]

}

EOF
#
systemctl restart kubelet
#
kubectl get nodes
```
