# [#](https://funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#配置-kubeadm)配置 kubeadm

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置 kubeadm](https://www.bilibili.com/video/av52359802/?p=5)

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#概述)概述

安装 kubernetes 主要是安装它的各个镜像，而 kubeadm 已经为我们集成好了运行 kubernetes 所需的基本镜像。但由于国内的网络原因，在搭建环境时，无法拉取到这些镜像。此时我们只需要修改为阿里云提供的镜像服务即可解决该问题。

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#创建并修改配置)创建并修改配置

```bash
# 导出配置文件
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

1
2

```yaml
# 修改配置为如下内容
apiVersion: kubeadm.k8s.io/v1beta1
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  # 修改为主节点 IP
  advertiseAddress: 192.168.141.130
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: ""
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
# 国内不能访问 Google，修改为阿里云
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
# 修改版本号
kubernetesVersion: v1.14.1
networking:
  dnsDomain: cluster.local
  # 配置成 Calico 的默认网段
  podSubnet: "192.168.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
# 开启 IPVS 模式
apiVersion: kubeproxy.config.k8s.io/v1alpha1
kind: KubeProxyConfiguration
featureGates:
  SupportIPVSProxyMode: true
mode: ipvs
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
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-kubeadm.html#查看和拉取镜像)查看和拉取镜像

```bash
# 查看所需镜像列表
kubeadm config images list --config kubeadm.yml
# 拉取镜像
kubeadm config images pull --config kubeadm.yml
```

1
2
3
4

![img](https://funtl.com/assets1/Lusifer_20190510144633.png)

上次更新: 2019-5-16 0:47:58

← [安装 kubeadm](https://funtl.com/zh/service-mesh-kubernetes/安装-kubeadm.html)[使用 kubeadm 搭建 kubernetes 集群 ](https://funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html)→