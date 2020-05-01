# [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#kubectl-常用命令)Kubectl 常用命令

> **小提示：** 所有命令前都可以加上 `watch` 命令来观察状态的实时变化，如：`watch kubectl get pods --all-namespaces`

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看组件状态)查看组件状态

```bash
kubectl get cs
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看环境信息)查看环境信息

```bash
kubectl cluster-info
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-node)查看 Node

```bash
kubectl get nodes -o wide
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看集群配置)查看集群配置

```bash
kubectl -n kube-system get cm kubeadm-config -oyaml
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#运行容器)运行容器

```bash
kubectl run nginx --image=nginx --replicas=2 --port=80
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#暴露服务)暴露服务

```bash
kubectl expose deployment nginx --port=80 --type=LoadBalancer
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看命名空间)查看命名空间

```bash
kubectl get namespace
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#创建命名空间)创建命名空间

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

1
2
3
4

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看容器)查看容器

```bash
kubectl get pods -o wide
kubectl get deployment -o wide
```

1
2

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看服务)查看服务

```bash
kubectl get service -o wide
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看详情)查看详情

```bash
kubectl describe pod <Pod Name>
kubectl describe deployment <Deployment Name>
kubectl describe service <Service Name>
```

1
2
3

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看日志)查看日志

```bash
kubectl logs -f <Pod Name>
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#删除容器和服务)删除容器和服务

```bash
kubectl delete deployment <Deployment Name>
kubectl delete service <Service Name>
```

1
2

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#配置方式运行)配置方式运行

```bash
kubectl create -f <YAML>
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#配置方式删除)配置方式删除

```bash
kubectl delete -f <YAML>
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看配置)查看配置

```bash
kubeadm config view
kubectl config view
```

1
2

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-ingress)查看 Ingress

```bash
kubectl get ingress
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看持久卷)查看持久卷

```bash
kubectl get pv
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看持久卷消费者)查看持久卷消费者

```bash
kubectl get pvc
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#查看-configmap)查看 ConfigMap

```bash
kubectl get cm <ConfigMap Name>
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/kubectl-常用命令.html#修改-configmap)修改 ConfigMap

```bash
kubectl edit cm <ConfigMap Name>
```

1

上次更新: 2019-6-10 2:43:32

← [Kubectl 与 Docker 命令](https://funtl.com/zh/service-mesh-kubernetes/命令-kubectl-与-docker.html)