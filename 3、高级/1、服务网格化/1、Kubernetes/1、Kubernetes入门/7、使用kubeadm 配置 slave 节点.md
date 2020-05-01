# [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#使用-kubeadm-配置-slave-节点)使用 kubeadm 配置 slave 节点

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#本节视频)本节视频

[【（千锋教育）服务网格化 Service Mesh】Kubernetes-配置 Slave](https://www.bilibili.com/video/av52359802/?p=7)

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#概述)概述

将 slave 节点加入到集群中很简单，只需要在 slave 服务器上安装 kubeadm，kubectl，kubelet 三个工具，然后使用 `kubeadm join` 命令加入即可。准备工作如下：

- 修改主机名
- 配置软件源
- 安装三个工具

由于之前章节已经说明了操作步骤，此处不再赘述。

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#将-slave-加入到集群)将 slave 加入到集群

```bash
kubeadm join 192.168.141.130:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:cab7c86212535adde6b8d1c7415e81847715cfc8629bb1d270b601744d662515

# 安装成功将看到如下信息
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

说明：

- token
  - 可以通过安装 master 时的日志查看 token 信息
  - 可以通过 `kubeadm token list` 命令打印出 token 信息
  - 如果 token 过期，可以使用 `kubeadm token create` 命令创建新的 token
- discovery-token-ca-cert-hash
  - 可以通过安装 master 时的日志查看 sha256 信息
  - 可以通过 `openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'` 命令查看 sha256 信息

以上方式感谢群友 **停 驻** 提供。

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#验证是否成功)验证是否成功

回到 master 服务器

```bash
kubectl get nodes

# 可以看到 slave 成功加入 master
NAME                STATUS     ROLES    AGE   VERSION
kubernetes-master   NotReady   master   9h    v1.14.1
kubernetes-slave1   NotReady   <none>   22s   v1.14.1
```

1
2
3
4
5
6

> 如果 slave 节点加入 master 时配置有问题可以在 slave 节点上使用 `kubeadm reset` 重置配置再使用 `kubeadm join` 命令重新加入即可。希望在 master 节点删除 node ，可以使用 `kubeadm delete nodes ` 删除。

## [#](https://funtl.com/zh/service-mesh-kubernetes/配置-Slave.html#查看-pod-状态)查看 pod 状态

```bash
kubectl get pod -n kube-system -o wide

NAME                                        READY   STATUS    RESTARTS   AGE   IP                NODE                NOMINATED NODE   READINESS GATES
coredns-8686dcc4fd-gwrmb                    0/1     Pending   0          9h    <none>            <none>              <none>           <none>
coredns-8686dcc4fd-j6gfk                    0/1     Pending   0          9h    <none>            <none>              <none>           <none>
etcd-kubernetes-master                      1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-apiserver-kubernetes-master            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-controller-manager-kubernetes-master   1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-proxy-496dr                            1/1     Running   0          17m   192.168.141.131   kubernetes-slave1   <none>           <none>
kube-proxy-rsnb6                            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
kube-scheduler-kubernetes-master            1/1     Running   1          9h    192.168.141.130   kubernetes-master   <none>           <none>
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

由此可以看出 coredns 尚未运行，此时我们还需要安装网络插件。

上次更新: 2019-5-16 0:47:58

← [使用 kubeadm 搭建 kubernetes 集群](https://funtl.com/zh/service-mesh-kubernetes/使用-kubeadm.html)[配置网络 ](https://funtl.com/zh/service-mesh-kubernetes/配置网络.html)→