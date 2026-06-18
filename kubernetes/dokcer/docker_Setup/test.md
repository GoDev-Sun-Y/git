## 2.2.3 安装docker

### 1 切换镜像源
```bash
[root@master ~]# wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
```
### 2 查看当前镜像源中支持的docker版本
```bash
[root@master ~]# yum list docker-ce --showduplicates
```
### 3 安装特定版本的docker-ce
*必须指定 `--setopt=obsoletes=0`，否则yum会自动安装更高版本*
```bash
[root@master ~]# yum install --setopt=obsoletes=0 docker-ce-18.06.3.ce-3.el7 -y
```

### 4 添加一个配置文件
*Docker在默认情况下使用的Cgroup Driver为cgroupfs，而kubernetes推荐使用systemd来代替cgroupfs*  
```bash
[root@master ~]# mkdir /etc/docker  

[root@master ~]# cat <<EOF > /etc/docker/daemon.json  

{  
  "exec-opts": ["native.cgroupdriver=systemd"],  
  "registry-mirrors":  
  ["https://kn0t2bca.mirror.aliyuncs.com"]  
}  

EOF
```
检查一下
```bash
more /etc/docker/daemon.json
```
### 5 启动docker
```bash
[root@master ~]# systemctl restart docker  
[root@master ~]# systemctl enable docker
```
### 6 检查docker状态和版本
```bash
[root@master ~]# docker version
```