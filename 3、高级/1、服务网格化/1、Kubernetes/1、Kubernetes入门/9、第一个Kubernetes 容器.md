# [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#第一个-kubernetes-容器)第一个 Kubernetes 容器

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-运行第一个容器](https://www.bilibili.com/video/av52359802/?p=10)

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查组件运行状态)检查组件运行状态

```bash
kubectl get cs

# 输出如下
NAME                 STATUS    MESSAGE             ERROR
# 调度服务，主要作用是将 POD 调度到 Node
scheduler            Healthy   ok                  
# 自动化修复服务，主要作用是 Node 宕机后自动修复 Node 回到正常的工作状态
controller-manager   Healthy   ok                  
# 服务注册与发现
etcd-0               Healthy   {"health":"true"} 
```

1
2
3
4
5
6
7
8
9
10

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查-master-状态)检查 Master 状态

```bash
kubectl cluster-info

# 输出如下
# 主节点状态
Kubernetes master is running at https://192.168.141.130:6443
# DNS 状态
KubeDNS is running at https://192.168.141.130:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

1
2
3
4
5
6
7
8
9

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#检查-nodes-状态)检查 Nodes 状态

```bash
kubectl get nodes

# 输出如下，STATUS 为 Ready 即为正常状态
NAME                STATUS   ROLES    AGE     VERSION
kubernetes-master   Ready    master   44h     v1.14.1
kubernetes-slave1   Ready    <none>   3h38m   v1.14.1
kubernetes-slave2   Ready    <none>   3h37m   v1.14.1
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#运行第一个容器实例)运行第一个容器实例

```bash
# 使用 kubectl 命令创建两个监听 80 端口的 Nginx Pod（Kubernetes 运行容器的最小单元）
kubectl run nginx --image=nginx --replicas=2 --port=80

# 输出如下
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created
```

1
2
3
4
5
6

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看全部-pods-的状态)查看全部 Pods 的状态

```bash
kubectl get pods

# 输出如下，需要等待一小段实践，STATUS 为 Running 即为运行成功
NAME                     READY   STATUS    RESTARTS   AGE
nginx-755464dd6c-qnmwp   1/1     Running   0          90m
nginx-755464dd6c-shqrp   1/1     Running   0          90m
```

1
2
3
4
5
6

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看已部署的服务)查看已部署的服务

```bash
kubectl get deployment

# 输出如下
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   2/2     2            2           91m
```

1
2
3
4
5

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#映射服务，让用户可以访问)映射服务，让用户可以访问

```bash
kubectl expose deployment nginx --port=80 --type=LoadBalancer

# 输出如下
service/nginx exposed
```

1
2
3
4

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看已发布的服务)查看已发布的服务

```bash
kubectl get services

# 输出如下
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        44h
# 由此可见，Nginx 服务已成功发布并将 80 端口映射为 31738
nginx        LoadBalancer   10.108.121.244   <pending>     80:31738/TCP   88m
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#查看服务详情)查看服务详情

```bash
kubectl describe service nginx

# 输出如下
Name:                     nginx
Namespace:                default
Labels:                   run=nginx
Annotations:              <none>
Selector:                 run=nginx
Type:                     LoadBalancer
IP:                       10.108.121.244
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  31738/TCP
Endpoints:                192.168.17.5:80,192.168.8.134:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#验证是否成功)验证是否成功

通过浏览器访问 Master 服务器

```text
http://192.168.141.130:31738/
```

1

此时 Kubernetes 会以负载均衡的方式访问部署的 Nginx 服务，能够正常看到 Nginx 的欢迎页即表示成功。容器实际部署在其它 Node 节点上，通过访问 Node 节点的 IP:Port 也是可以的。

## [#](https://funtl.com/zh/service-mesh-kubernetes/第一个-Kubernetes-容器.html#停止服务)停止服务

```bash
kubectl delete deployment nginx

# 输出如下
deployment.extensions "nginx" deleted
```

1
2
3
4

```bash
kubectl delete service nginx

# 输出如下
service "nginx" deleted
```

1
2
3
4

上次更新: 2019-5-16 7:31:04

← [配置网络](https://funtl.com/zh/service-mesh-kubernetes/配置网络.html)[概念总结 ](https://funtl.com/zh/service-mesh-kubernetes/概念总结.html)→