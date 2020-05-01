# [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#解决-node-无法加入的问题)解决 Node 无法加入的问题

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-解决 Node 无法加入的问题](https://www.bilibili.com/video/av52359802/?p=14)

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#问题描述)问题描述

当我们使用 `kubeadm join` 命令将 Node 节点加入集群时，你会发现所有 `kubectl` 命令均不可用（呈现阻塞状态，并不会返回响应结果），我们可以在 Node 节点中通过 `kubeadm reset` 命令将 Node 节点下线，此时回到 Master 节点再使用 `watch kubectl get pods --all-namespaces` 可以看到下图中报错了，`coredns-xxx-xxx` 状态为 `CrashLoopBackOff`

![img](https://funtl.com/assets1/Lusifer_20190604010905.png)

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#解决方案)解决方案

从上面的错误信息不难看出应该是出现了网络问题，而我们在安装过程中只使用了一个网络插件 **Calico** ，那么该错误是不是由 Calico 引起的呢？带着这个疑问我们去到 Calico 官网再看一下它的说明，官网地址：https://docs.projectcalico.org/v3.7/getting-started/kubernetes/

在它的 Quickstart 里有两段话（属于特别提醒），截图如下：

![img](https://funtl.com/assets1/Lusifer_20190604013518.png)

上面这段话的主要意思是：当 `kubeadm` 安装完成后不要关机，继续完成后续的安装步骤；这也说明了安装 Kubernetes 的过程不要出现中断一口气搞定（不过这不是重点）(*￣rǒ￣)

![img](https://funtl.com/assets1/Lusifer_20190604014207.png)

上面这段话的主要意思是：如果你的网络在 `192.168.0.0/16` 网段中，则必须选择一个不同的 Pod 网络；恰巧咱们的网络范围（我虚拟机的 IP 范围是 `192.168.141.0/24`）和该网段重叠 (ノへ￣、)；好吧，当时做单节点集群时因为没啥问题而忽略了 ♪(^∇^*)

so，能够遇到这个问题主要是因为虚拟机 IP 范围刚好和 Calico 默认网段重叠导致的，所以想要解决这个问题，咱们就需要修改 Calico 的网段了（当然也可以改虚拟机的），换句话说就是大家重装一下 o (一︿一 +) o

**按照以下标准步骤重装即可**

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#重置-kubernetes)重置 Kubernetes

```bash
kubeadm reset

# 输出如下
[reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0604 01:55:28.517280   22688 reset.go:234] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] No etcd config found. Assuming external etcd
[reset] Please manually reset etcd to prevent further issues
[reset] Stopping the kubelet service
[reset] unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of stateful directories: [/var/lib/kubelet /etc/cni/net.d /var/lib/dockershim /var/run/kubernetes]
[reset] Deleting contents of config directories: [/etc/kubernetes/manifests /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually.
For example:
iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.
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

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#删除-kubectl-配置)删除 kubectl 配置

```bash
rm -fr ~/.kube/
```

1

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#启用-ipvs)启用 IPVS

```bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
```

1
2
3
4
5

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#导出并修改配置文件)导出并修改配置文件

```bash
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

1

配置文件修改如下

```yaml
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
  advertiseAddress: 192.168.141.150
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master-01
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta1
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controlPlaneEndpoint: "192.168.141.200:6444"
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.14.2
networking:
  dnsDomain: cluster.local
  # 主要修改在这里，替换 Calico 网段为我们虚拟机不重叠的网段（这里用的是 Flannel 默认网段）
  podSubnet: "10.244.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}
---
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

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#kubeadm-初始化)kubeadm 初始化

```bash
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log

# 输出如下
[init] Using Kubernetes version: v1.14.2
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kubernetes-master-01 localhost] and IPs [192.168.141.150 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kubernetes-master-01 localhost] and IPs [192.168.141.150 127.0.0.1 ::1]
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes-master-01 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.141.150 192.168.141.200]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "admin.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 24.507568 seconds
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.14" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Storing the certificates in ConfigMap "kubeadm-certs" in the "kube-system" Namespace
[upload-certs] Using certificate key:
a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a
[mark-control-plane] Marking the node kubernetes-master-01 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kubernetes-master-01 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: abcdef.0123456789abcdef
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of the control-plane node running the following command on each as root:

  kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad \
    --experimental-control-plane --certificate-key a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a

Please note that the certificate-key gives access to cluster sensitive data, keep it secret!
As a safeguard, uploaded-certs will be deleted in two hours; If necessary, you can use 
"kubeadm init phase upload-certs --experimental-upload-certs" to reload certs afterward.

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad 
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
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#配置-kubectl)配置 kubectl

```bash
# 配置 kubectl
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# 验证是否成功
kubectl get node
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#下载-calico-配置文件并修改)下载 Calico 配置文件并修改

```bash
wget https://docs.projectcalico.org/v3.7/manifests/calico.yaml
```

1

```bash
vi calico.yaml
```

1

修改第 611 行，将 `192.168.0.0/16` 修改为 `10.244.0.0/16`，可以通过如下命令快速查找

- 显示行号：`:set number`
- 查找字符：`/要查找的字符`，输入小写 `n` 下一个匹配项，输入大写 `N` 上一个匹配项

![img](https://funtl.com/assets1/Lusifer_20190604022029.png)

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#安装-calico)安装 Calico

```bash
kubectl apply -f calico.yaml

# 输出如下
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.extensions/calico-node created
serviceaccount/calico-node created
deployment.extensions/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
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

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#加入-master-节点)加入 Master 节点

```bash
# 示例如下，别忘记两个备用节点都要加入哦
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad \
    --experimental-control-plane --certificate-key a662b8364666f82c93cc5cd4fb4fabb623bbe9afdb182da353ac40f1752dfa4a

# 输出如下
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[preflight] Running pre-flight checks before initializing the new control plane instance
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[download-certs] Downloading the certificates in Secret "kubeadm-certs" in the "kube-system" Namespace
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [kubernetes-master-02 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.141.151 192.168.141.200]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [kubernetes-master-02 localhost] and IPs [192.168.141.151 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [kubernetes-master-02 localhost] and IPs [192.168.141.151 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Valid certificates and keys now exist in "/etc/kubernetes/pki"
[certs] Using the existing "sa" key
[kubeconfig] Generating kubeconfig files
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[endpoint] WARNING: port specified in controlPlaneEndpoint overrides bindPort in the controlplane address
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[check-etcd] Checking that the etcd cluster is healthy
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...
[etcd] Announced new etcd member joining to the existing etcd cluster
[etcd] Wrote Static Pod manifest for a local etcd member to "/etc/kubernetes/manifests/etcd.yaml"
[etcd] Waiting for the new etcd member to join the cluster. This can take up to 40s
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[mark-control-plane] Marking the node kubernetes-master-02 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node kubernetes-master-02 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]

This node has joined the cluster and a new control plane instance was created:

* Certificate signing request was sent to apiserver and approval was received.
* The Kubelet was informed of the new secure connection details.
* Control plane (master) label and taint were applied to the new node.
* The Kubernetes control plane instances scaled up.
* A new etcd member was added to the local/stacked etcd cluster.

To start administering your cluster from this node, you need to run the following as a regular user:

        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config

Run 'kubectl get nodes' to see this node join the cluster.
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
53
54
55
56
57
58
59
60
61
62
63
64
65
66

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#加入-node-节点)加入 Node 节点

```bash
# 示例如下
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad

# 输出如下
>     --discovery-token-ca-cert-hash sha256:2ea8c138021fb1e184a24ed2a81c16c92f9f25c635c73918b1402df98f9c8aad 
[preflight] Running pre-flight checks
        [WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
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

## [#](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html#验证是否可用)验证是否可用

```bash
kubectl get node

# 输出如下，我们可以看到 Node 节点已经成功上线 ━━(￣ー￣*|||━━
NAME                   STATUS   ROLES    AGE     VERSION
kubernetes-master-01   Ready    master   19m     v1.14.2
kubernetes-master-02   Ready    master   4m46s   v1.14.2
kubernetes-master-03   Ready    master   3m23s   v1.14.2
kubernetes-node-01     Ready    <none>   74s     v1.14.2
```

1
2
3
4
5
6
7
8

```bash
watch kubectl get pods --all-namespaces

# 输出如下，coredns 也正常运行了
Every 2.0s: kubectl get pods --all-namespaces                                                                                                 kubernetes-master-01: Tue Jun  4 02:31:43 2019

NAMESPACE     NAME                                           READY   STATUS    RESTARTS   AGE
kube-system   calico-kube-controllers-8646dd497f-hz5xp       1/1     Running   0          9m9s
kube-system   calico-node-2z892                              1/1     Running   0          9m9s
kube-system   calico-node-fljxv                              1/1     Running   0          6m39s
kube-system   calico-node-vprlw                              1/1     Running   0          5m16s
kube-system   calico-node-xvqcx                              1/1     Running   0          3m7s
kube-system   coredns-8686dcc4fd-5ndjm                       1/1     Running   0          21m
kube-system   coredns-8686dcc4fd-zxtql                       1/1     Running   0          21m
kube-system   etcd-kubernetes-master-01                      1/1     Running   0          20m
kube-system   etcd-kubernetes-master-02                      1/1     Running   0          6m37s
kube-system   etcd-kubernetes-master-03                      1/1     Running   0          5m14s
kube-system   kube-apiserver-kubernetes-master-01            1/1     Running   0          20m
kube-system   kube-apiserver-kubernetes-master-02            1/1     Running   0          6m37s
kube-system   kube-apiserver-kubernetes-master-03            1/1     Running   0          5m14s
kube-system   kube-controller-manager-kubernetes-master-01   1/1     Running   1          20m
kube-system   kube-controller-manager-kubernetes-master-02   1/1     Running   0          6m37s
kube-system   kube-controller-manager-kubernetes-master-03   1/1     Running   0          5m14s
kube-system   kube-proxy-68jqr                               1/1     Running   0          3m7s
kube-system   kube-proxy-69bnn                               1/1     Running   0          6m39s
kube-system   kube-proxy-vvhp5                               1/1     Running   0          5m16s
kube-system   kube-proxy-ws6wx                               1/1     Running   0          21m
kube-system   kube-scheduler-kubernetes-master-01            1/1     Running   1          20m
kube-system   kube-scheduler-kubernetes-master-02            1/1     Running   0          6m37s
kube-system   kube-scheduler-kubernetes-master-03            1/1     Running   0          5m14s
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

**至此，Kubernetes 高可用集群算是彻底部署成功，撒花撒花 (゜-゜)つロ 干杯**

上次更新: 2019-6-4 4:34:26

← [Kubernetes 高可用集群](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html)[通过资源配置运行容器 ](https://funtl.com/zh/service-mesh-kubernetes/通过资源配置运行容器.html)→