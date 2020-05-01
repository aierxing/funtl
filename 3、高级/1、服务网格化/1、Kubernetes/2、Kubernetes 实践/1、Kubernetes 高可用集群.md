# [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#kubernetes-高可用集群)Kubernetes 高可用集群

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#本节视频)本节视频

- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-高可用集群1](https://www.bilibili.com/video/av52359802/?p=12)
- [【（千锋教育）服务网格化 Service Mesh】Kubernetes-高可用集群2](https://www.bilibili.com/video/av52359802/?p=13)

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#概述)概述

在入门课程中我们部署的 Kubernetes 是 **集群模式**，但在实际生产中我们需要部署 **高可用集群** ，本章内容旨在指导大家如何部署 Kubernetes 高可用集群

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#统一环境配置)统一环境配置

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#节点配置)节点配置

| 主机名               | IP              | 角色   | 系统                | CPU/内存 | 磁盘 |
| -------------------- | --------------- | ------ | ------------------- | -------- | ---- |
| kubernetes-master-01 | 192.168.141.150 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-master-02 | 192.168.141.151 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-master-03 | 192.168.141.152 | Master | Ubuntu Server 18.04 | 2核2G    | 20G  |
| kubernetes-node-01   | 192.168.141.160 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| kubernetes-node-02   | 192.168.141.161 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| kubernetes-node-03   | 192.168.141.162 | Node   | Ubuntu Server 18.04 | 2核4G    | 20G  |
| Kubernetes VIP       | 192.168.141.200 | -      | -                   | -        | -    |

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#对操作系统的配置)对操作系统的配置

> 特别注意：以下步骤请在制作 VMware 镜像时一并完成，避免逐台安装的痛苦

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#关闭交换空间)关闭交换空间

```bash
swapoff -a
```

1

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#避免开机启动交换空间)避免开机启动交换空间

```bash
# 注释 swap 开头的行
vi /etc/fstab
```

1
2

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#关闭防火墙)关闭防火墙

```bash
ufw disable
```

1

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-dns)配置 DNS

```bash
# 取消 DNS 行注释，并增加 DNS 配置如：114.114.114.114，修改后重启下计算机
vi /etc/systemd/resolved.conf
```

1
2

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-docker)安装 Docker

```bash
# 更新软件源
sudo apt-get update
# 安装所需依赖
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# 安装 GPG 证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# 新增软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# 再次更新软件源
sudo apt-get -y update
# 安装 Docker CE 版
sudo apt-get -y install docker-ce
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

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-docker-加速器)配置 Docker 加速器

> 特别注意：国内镜像加速器可能会很卡，请替换成你自己阿里云镜像加速器，地址如：`https://yourself.mirror.aliyuncs.com`，在阿里云控制台的 **容器镜像服务 -> 镜像加速器** 菜单中可以找到

在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）

```json
{
  "registry-mirrors": [
    "https://registry.docker-cn.com"
  ]
}
```

1
2
3
4
5

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-kubeadm，kubelet，kubectl)安装 kubeadm，kubelet，kubectl

```bash
# 安装系统工具
apt-get update && apt-get install -y apt-transport-https

# 安装 GPG 证书
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

# 写入软件源；注意：我们用系统代号为 bionic，但目前阿里云不支持，所以沿用 16.04 的 xenial
cat << EOF >/etc/apt/sources.list.d/kubernetes.list
> deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
> EOF

# 安装
apt-get update && apt-get install -y kubelet kubeadm kubectl
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

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#同步时间)同步时间

**设置时区**

```bash
dpkg-reconfigure tzdata
```

1

选择 **Asia（亚洲）**

![img](https://funtl.com/assets1/Lusifer_20190602220034.png)

选择 **Shanghai（上海）**

![img](https://funtl.com/assets1/Lusifer_20190602220202.png)

**时间同步**

```bash
# 安装 ntpdate
apt-get install ntpdate

# 设置系统时间与网络时间同步（cn.pool.ntp.org 位于中国的公共 NTP 服务器）
ntpdate cn.pool.ntp.org

# 将系统时间写入硬件时间
hwclock --systohc
```

1
2
3
4
5
6
7
8

**确认时间**

```bash
date

# 输出如下（自行对照与系统时间是否一致）
Sun Jun  2 22:02:35 CST 2019
```

1
2
3
4

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-ipvs)配置 IPVS

```bash
# 安装系统工具
apt-get install -y ipset ipvsadm

# 配置并加载 IPVS 模块
mkdir -p /etc/sysconfig/modules/
vi /etc/sysconfig/modules/ipvs.modules

# 输入如下内容
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4

# 执行脚本，注意：如果重启则需要重新运行该脚本
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4

# 执行脚本输出如下
ip_vs_sh               16384  0
ip_vs_wrr              16384  0
ip_vs_rr               16384  0
ip_vs                 147456  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
nf_conntrack_ipv4      16384  3
nf_defrag_ipv4         16384  1 nf_conntrack_ipv4
nf_conntrack          131072  8 xt_conntrack,nf_nat_masquerade_ipv4,nf_conntrack_ipv4,nf_nat,ipt_MASQUERADE,nf_nat_ipv4,nf_conntrack_netlink,ip_vs
libcrc32c              16384  4 nf_conntrack,nf_nat,raid456,ip_vs
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

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置内核参数)配置内核参数

```bash
# 配置参数
vi /etc/sysctl.d/k8s.conf

# 输入如下内容
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_nonlocal_bind = 1
net.ipv4.ip_forward = 1
vm.swappiness=0

# 应用参数
sysctl --system

# 应用参数输出如下（找到 Applying /etc/sysctl.d/k8s.conf 开头的日志）
* Applying /etc/sysctl.d/10-console-messages.conf ...
kernel.printk = 4 4 1 7
* Applying /etc/sysctl.d/10-ipv6-privacy.conf ...
* Applying /etc/sysctl.d/10-kernel-hardening.conf ...
kernel.kptr_restrict = 1
* Applying /etc/sysctl.d/10-link-restrictions.conf ...
fs.protected_hardlinks = 1
fs.protected_symlinks = 1
* Applying /etc/sysctl.d/10-lxd-inotify.conf ...
fs.inotify.max_user_instances = 1024
* Applying /etc/sysctl.d/10-magic-sysrq.conf ...
kernel.sysrq = 176
* Applying /etc/sysctl.d/10-network-security.conf ...
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.tcp_syncookies = 1
* Applying /etc/sysctl.d/10-ptrace.conf ...
kernel.yama.ptrace_scope = 1
* Applying /etc/sysctl.d/10-zeropage.conf ...
vm.mmap_min_addr = 65536
* Applying /usr/lib/sysctl.d/50-default.conf ...
net.ipv4.conf.all.promote_secondaries = 1
net.core.default_qdisc = fq_codel
* Applying /etc/sysctl.d/99-sysctl.conf ...
* Applying /etc/sysctl.d/k8s.conf ...
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_nonlocal_bind = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
* Applying /etc/sysctl.conf ...
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

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#修改-cloud-cfg)修改 cloud.cfg

```bash
vi /etc/cloud/cloud.cfg

# 该配置默认为 false，修改为 true 即可
preserve_hostname: true
```

1
2
3
4

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#单独节点配置)单独节点配置

> 特别注意：为 Master 和 Node 节点单独配置对应的 **IP** 和 **主机名**

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置-ip)配置 IP

编辑 `vi /etc/netplan/50-cloud-init.yaml` 配置文件，修改内容如下

```yaml
network:
    ethernets:
        ens33:
          # 我的 Master 是 150 - 152，Node 是 160 - 162
          addresses: [192.168.141.150/24]
          gateway4: 192.168.141.2
          nameservers:
            addresses: [192.168.141.2]
    version: 2
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

使用 `netplan apply` 命令让配置生效

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#配置主机名)配置主机名

```bash
# 修改主机名
hostnamectl set-hostname kubernetes-master-01

# 配置 hosts
cat >> /etc/hosts << EOF
192.168.141.150 kubernetes-master-01
EOF
```

1
2
3
4
5
6
7

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#安装-haproxy-keepalived)安装 HAProxy + Keepalived

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#概述-2)概述

Kubernetes Master 节点运行组件如下：

- **kube-apiserver：** 提供了资源操作的唯一入口，并提供认证、授权、访问控制、API 注册和发现等机制
- **kube-scheduler：** 负责资源的调度，按照预定的调度策略将 Pod 调度到相应的机器上
- **kube-controller-manager：** 负责维护集群的状态，比如故障检测、自动扩展、滚动更新等
- **etcd：** CoreOS 基于 Raft 开发的分布式 key-value 存储，可用于服务发现、共享配置以及一致性保障（如数据库选主、分布式锁等）

`kube-scheduler` 和 `kube-controller-manager` 可以以集群模式运行，通过 leader 选举产生一个工作进程，其它进程处于阻塞模式。

**`kube-apiserver` 可以运行多个实例，但对其它组件需要提供统一的访问地址，本章节部署 Kubernetes 高可用集群实际就是利用 HAProxy + Keepalived 配置该组件**

配置的思路就是利用 HAProxy + Keepalived 实现 `kube-apiserver` 虚拟 IP 访问从而实现高可用和负载均衡，拆解如下：

- Keepalived 提供 `kube-apiserver` 对外服务的虚拟 IP（VIP）
- HAProxy 监听 Keepalived VIP
- 运行 Keepalived 和 HAProxy 的节点称为 LB（负载均衡） 节点
- Keepalived 是一主多备运行模式，故至少需要两个 LB 节点
- Keepalived 在运行过程中周期检查本机的 HAProxy 进程状态，如果检测到 HAProxy 进程异常，则触发重新选主的过程，VIP 将飘移到新选出来的主节点，从而实现 VIP 的高可用
- 所有组件（如 kubeclt、apiserver、controller-manager、scheduler 等）都通过 VIP +HAProxy 监听的 6444 端口访问 `kube-apiserver` 服务（**注意：`kube-apiserver` 默认端口为 6443，为了避免冲突我们将 HAProxy 端口设置为 6444，其它组件都是通过该端口统一请求 apiserver**）

![负载均衡架构图](https://funtl.com/assets1/20190427104124213.png)

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#创建-haproxy-启动脚本)创建 HAProxy 启动脚本

> 该步骤在 `kubernetes-master-01` 执行

```bash
mkdir -p /usr/local/kubernetes/lb
vi /usr/local/kubernetes/lb/start-haproxy.sh

# 输入内容如下
#!/bin/bash
# 修改为你自己的 Master 地址
MasterIP1=192.168.141.150
MasterIP2=192.168.141.151
MasterIP3=192.168.141.152
# 这是 kube-apiserver 默认端口，不用修改
MasterPort=6443

# 容器将 HAProxy 的 6444 端口暴露出去
docker run -d --restart=always --name HAProxy-K8S -p 6444:6444 \
        -e MasterIP1=$MasterIP1 \
        -e MasterIP2=$MasterIP2 \
        -e MasterIP3=$MasterIP3 \
        -e MasterPort=$MasterPort \
        wise2c/haproxy-k8s

# 设置权限
chmod +x start-haproxy.sh
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

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#创建-keepalived-启动脚本)创建 Keepalived 启动脚本

> 该步骤在 `kubernetes-master-01` 执行

```bash
mkdir -p /usr/local/kubernetes/lb
vi /usr/local/kubernetes/lb/start-keepalived.sh

# 输入内容如下
#!/bin/bash
# 修改为你自己的虚拟 IP 地址
VIRTUAL_IP=192.168.141.200
# 虚拟网卡设备名
INTERFACE=ens33
# 虚拟网卡的子网掩码
NETMASK_BIT=24
# HAProxy 暴露端口，内部指向 kube-apiserver 的 6443 端口
CHECK_PORT=6444
# 路由标识符
RID=10
# 虚拟路由标识符
VRID=160
# IPV4 多播地址，默认 224.0.0.18
MCAST_GROUP=224.0.0.18

docker run -itd --restart=always --name=Keepalived-K8S \
        --net=host --cap-add=NET_ADMIN \
        -e VIRTUAL_IP=$VIRTUAL_IP \
        -e INTERFACE=$INTERFACE \
        -e CHECK_PORT=$CHECK_PORT \
        -e RID=$RID \
        -e VRID=$VRID \
        -e NETMASK_BIT=$NETMASK_BIT \
        -e MCAST_GROUP=$MCAST_GROUP \
        wise2c/keepalived-k8s

# 设置权限
chmod +x start-keepalived.sh
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

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#复制脚本到其它-master-地址)复制脚本到其它 Master 地址

分别在 `kubernetes-master-02` 和 `kubernetes-master-03` 执行创建工作目录命令

```bash
mkdir -p /usr/local/kubernetes/lb
```

1

将 `kubernetes-master-01` 中的脚本拷贝至其它 Master

```bash
scp start-haproxy.sh start-keepalived.sh 192.168.141.151:/usr/local/kubernetes/lb
scp start-haproxy.sh start-keepalived.sh 192.168.141.152:/usr/local/kubernetes/lb
```

1
2

分别在 3 个 Master 中启动容器（执行脚本）

```bash
sh /usr/local/kubernetes/lb/start-haproxy.sh && sh /usr/local/kubernetes/lb/start-keepalived.sh
```

1

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证是否成功)验证是否成功

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#查看容器)查看容器

```bash
docker ps

# 输出如下
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                    NAMES
f50df479ecae        wise2c/keepalived-k8s   "/usr/bin/keepalived…"   About an hour ago   Up About an hour                             Keepalived-K8S
75066a7ed2fb        wise2c/haproxy-k8s      "/docker-entrypoint.…"   About an hour ago   Up About an hour    0.0.0.0:6444->6444/tcp   HAProxy-K8S
```

1
2
3
4
5
6

#### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#查看网卡绑定的虚拟-ip)查看网卡绑定的虚拟 IP

```bash
ip a | grep ens33

# 输出如下
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.141.151/24 brd 192.168.141.255 scope global ens33
    inet 192.168.141.200/24 scope global secondary ens33
```

1
2
3
4
5
6

> 特别注意：Keepalived 会对 HAProxy 监听的 6444 端口进行检测，如果检测失败即认定本机 HAProxy 进程异常，会将 VIP 漂移到其他节点，所以无论本机 Keepalived 容器异常或 HAProxy 容器异常都会导致 VIP 漂移到其他节点

## [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#部署-kubernetes-集群)部署 Kubernetes 集群

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#初始化-master)初始化 Master

- 创建工作目录并导出配置文件

```bash
# 创建工作目录
mkdir -p /usr/local/kubernetes/cluster

# 导出配置文件到工作目录
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
```

1
2
3
4
5

- 修改配置文件

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
  # 修改为主节点 IP
  advertiseAddress: 192.168.141.150
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
# 配置 Keepalived 地址和 HAProxy 端口
controlPlaneEndpoint: "192.168.141.200:6444"
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
kubernetesVersion: v1.14.2
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

- kubeadm 初始化

```bash
# kubeadm 初始化
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log

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
8
9
10

- 安装网络插件

```bash
# 安装 Calico
kubectl apply -f https://docs.projectcalico.org/v3.7/manifests/calico.yaml

# 验证安装是否成功
watch kubectl get pods --all-namespaces
```

1
2
3
4
5

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#加入-master-节点)加入 Master 节点

从 `kubeadm-init.log` 中获取命令，分别将 `kubernetes-master-02` 和 `kubernetes-master-03` 加入 Master

```bash
# 以下为示例命令
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
  --discovery-token-ca-cert-hash sha256:56d53268517c132ae81c868ce99c44be797148fb2923e59b49d73c99782ff21f \
  --experimental-control-plane --certificate-key c4d1525b6cce4b69c11c18919328c826f92e660e040a46f5159431d5ff0545bd
```

1
2
3
4

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#加入-node-节点)加入 Node 节点

从 `kubeadm-init.log` 中获取命令，分别将 `kubernetes-node-01` 至 `kubernetes-node-03` 加入 Node

```bash
# 以下为示例命令
kubeadm join 192.168.141.200:6444 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:56d53268517c132ae81c868ce99c44be797148fb2923e59b49d73c99782ff21f 
```

1
2
3

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证集群状态)验证集群状态

- 查看 Node

```bash
kubectl get nodes -o wide
```

1

- 查看 Pod

```bash
kubectl -n kube-system get pod -o wide
```

1

- 查看 Service

```bash
kubectl -n kube-system get svc
```

1

- 验证 IPVS

查看 kube-proxy 日志，server_others.go:176] Using ipvs Proxier.

```bash
kubectl -n kube-system logs -f <kube-proxy 容器名>
```

1

- 查看代理规则

```bash
ipvsadm -ln
```

1

- 查看生效的配置

```bash
kubectl -n kube-system get cm kubeadm-config -oyaml
```

1

- 查看 etcd 集群

```bash
kubectl -n kube-system exec etcd-kubernetes-master-01 -- etcdctl \
	--endpoints=https://192.168.141.150:2379 \
	--ca-file=/etc/kubernetes/pki/etcd/ca.crt \
	--cert-file=/etc/kubernetes/pki/etcd/server.crt \
	--key-file=/etc/kubernetes/pki/etcd/server.key cluster-health

# 输出如下
member 1dfaf07371bb0cb6 is healthy: got healthy result from https://192.168.141.152:2379
member 2da85730b52fbeb2 is healthy: got healthy result from https://192.168.141.150:2379
member 6a3153eb4faaaffa is healthy: got healthy result from https://192.168.141.151:2379
cluster is healthy
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

### [#](https://funtl.com/zh/service-mesh-kubernetes/高可用集群.html#验证高可用)验证高可用

> 特别注意：Keepalived 要求至少 2 个备用节点，故想测试高可用至少需要 1 主 2 从模式验证，否则可能出现意想不到的问题

对任意一台 Master 机器执行关机操作

```bash
shutdown -h now
```

1

在任意一台 Master 节点上查看 Node 状态

```bash
kubectl get node

# 输出如下，除已关机那台状态为 NotReady 其余正常便表示成功
NAME                   STATUS   ROLES    AGE   VERSION
kubernetes-master-01   NotReady master   18m   v1.14.2
kubernetes-master-02   Ready    master   17m   v1.14.2
kubernetes-master-03   Ready    master   16m   v1.14.2
```

1
2
3
4
5
6
7

查看 VIP 漂移

```bash
ip a |grep ens33

# 输出如下
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    inet 192.168.141.151/24 brd 192.168.141.255 scope global ens33
    inet 192.168.141.200/24 scope global secondary ens33
```

1
2
3
4
5
6

上次更新: 2019-6-4 4:48:22

← [概念总结](https://funtl.com/zh/service-mesh-kubernetes/概念总结.html)[解决 Node 无法加入的问题 ](https://funtl.com/zh/service-mesh-kubernetes/解决-Node-无法加入的问题.html)→