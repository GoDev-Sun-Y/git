# 2.3 服务部署

接下来在kubernetes集群中部署一个nginx程序，测试下集群是否在正常工作。

## 部署nginx
~~~bash

[root@master ~]# kubectl create deployment nginx --image=nginx:1.14-alpine
~~~

## 暴露端口
~~~bash

[root@master ~]# kubectl expose deployment nginx --port=80 --type=NodePort
~~~

## 查看服务状态
~~~bash

[root@master ~]# kubectl get pods,service
~~~

### Pods 列表：

| NAME                          | READY | STATUS   | RESTARTS | AGE  |
|-------------------------------|-------|----------|----------|------|
| pod/nginx-86c57db685-fdc2k    | 1/1   | Running  | 0        | 18m  |

### Services 列表：

| NAME             | TYPE       | CLUSTER-IP    | EXTERNAL-IP | PORT(S)        | AGE  |
|------------------|------------|---------------|-----------|----------------|------|
| service/kubernetes | ClusterIP  | 10.96.0.1     | <none>    | 443/TCP        | 82m  |
| service/nginx      | NodePort   | 10.104.121.45 | <none>    | 80:30073/TCP   | 17m  |

## 4 最后在电脑上访问下部署的nginx服务

> 不安全 | 192.168.109.101:30073
