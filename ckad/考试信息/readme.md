# killer.sh 是什么？

简单说：killer.sh 是 Linux Foundation（LF）/ CNCF 官方合作的 CKAD/CKA/CKS 考试模拟器平台，不是教学课，也不是 lab 练习场——它是一个高度还原真实考试环境的"全真模拟沙盒"

# kind 是什么？

 - 是真正的 K8s 集群，跑在 Docker 容器里，不是模拟器。
 - 适合：​ 练 CKAD 90% 的考点（Pod/Deploy/Service/ConfigMap 等）。
 - 不适合：​ 复杂的多机网络、底层存储硬件（考试也不怎么考这些）。
 # 替代 KodeKloud 的方案
- 既然注册卡住，直接用 Kind + 免费资料：
- GitHub:​ dgkanatsios/CKAD-exercises(必刷题库)。
- 浏览器：​ killercoda.com(免安装练手)。
# 验证环境

## 在本地执行，确认 kind 能跑：
```bash
# 1. 安装并启动集群
kind create cluster --name ckad
kubectl cluster-info
# 2. 跑一个Pod测试
kubectl run nginx --image=nginx --dry-run=client -o yaml > p.yaml
kubectl apply -f p.yaml
kubectl get pods
```
## 配置“考试同款”环境
一旦集群跑通，马上配置别名（极大提升速度）：
```bash
# 写入 ~/.bashrc
alias k='kubectl'
alias kgp='kubectl get pods'
export do='--dry-run=client -o yaml'
source ~/.bashrc
```