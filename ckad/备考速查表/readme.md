# 环境一键配置（Kind + Alias）
#  1. 安装 Kind 
```bash
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```
#  2. 创建集群 
```bash
kind create cluster --name ckad
```
# 3. 配置 Kubectl 别名 (复制到 ~/.bashrc) 
```bash
cat >> ~/.bashrc << 'EOF'
# CKAD Shortcuts
alias k='kubectl'
alias kgp='k get pods'
alias kgpa='k get pods -A'
alias kdp='k describe pod'
alias kl='k logs'
alias kaf='k apply -f'
alias kdel='k delete'
export do='--dry-run=client -o yaml'
EOF
```
# 生效配置
```bash
source ~/.bashrc
```
# 4. 开启 Kubectl 自动补全 
```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

# 必背 Imperative 命令（考试提速 50%）
# Pod / Deployment 
```bash
k run nginx --image=nginx $do > pod.yaml
k create deploy web --image=nginx --replicas=3 $do > deploy.yaml
```bash
# Service 
k expose pod nginx --port=80 --name=nginx-svc $do > svc.yaml
k expose deploy web --port=80 --target-port=8080 --type=NodePort $do > svc.yaml

#  ConfigMap / Secret 
k create cm my-config --from-literal=key1=value1 $do > cm.yaml
k create secret generic my-secret --from-literal=password=123 $do > secret.yaml
```
# 强制删除 (Pod 卡住时)
```bash
k delete pod nginx --force --grace-period=0
```
# 查看资源定义 (查文档用)
```bash
k explain pod.spec.containers
k explain deployment.spec.strategy
```

# vim 极简配置
```bash
set number          " 显示行号
set tabstop=2       " Tab 宽度为 2
set shiftwidth=2    " 自动缩进宽度为 2
set expandtab       " Tab 转为空格
set autoindent      " 自动缩进
set smartindent     " 智能缩进
set mouse-=a        " 禁用鼠标（防止误触）
syntax on           " 语法高亮
```

# 考前 10 分钟检查清单
```bash
# 1. 确认集群
kubectl get nodes
kubectl cluster-info

# 2. 确认当前 Context (非常重要！)
kubectl config current-context
kubectl config get-contexts

# 3. 设置默认命名空间 (如果题目要求)
kubectl config set-context --current --namespace=default

# 4. 测试编辑权限
k run test --image=nginx --dry-run=client -o yaml | k apply -f -
k delete pod test
```
# 刷题路径（GitHub 题库）
```bash
# 克隆必刷题库
git clone https://github.com/dgkanatsios/CKAD-exercises.git

# 按此顺序练习
cd CKAD-exercises

# 1. Core Concepts
# 2. Multi-Container Pods
# 3. Pod Design (Labels, Annotations, Deployments)
# 4. Configuration (ConfigMap, Secret)
# 5. Observability (Probes, Logs)
# 6. Services & Networking
# 7. State (Volumes, PVC)
# 8. Scheduling
```
