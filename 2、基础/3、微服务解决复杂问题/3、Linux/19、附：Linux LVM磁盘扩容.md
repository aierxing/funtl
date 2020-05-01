# [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#附：linux-lvm-磁盘扩容)附：Linux LVM 磁盘扩容

## [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#lvm-的基本概念)LVM 的基本概念

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#物理卷-physical-volume-pv)物理卷 Physical volume (PV)

可以在上面建立卷组的媒介，可以是硬盘分区，也可以是硬盘本身或者回环文件（loopback file）。物理卷包括一个特殊的 header，其余部分被切割为一块块物理区域（physical extents）。

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#卷组-volume-group-vg)卷组 Volume group (VG)

将一组物理卷收集为一个管理单元。

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#逻辑卷-logical-volume-lv)逻辑卷 Logical volume (LV)

虚拟分区，由物理区域（physical extents）组成。

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#物理区域-physical-extent-pe)物理区域 Physical extent (PE)

硬盘可供指派给逻辑卷的最小单位（通常为 4MB）。

## [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#磁盘操作相关命令)磁盘操作相关命令

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#df-h（查看挂载点）)`df -h`（查看挂载点）

![img](https://funtl.com/assets/Lusifer_201810310001.png)

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#lvdisplay（显示当前的-logical-volume）)`lvdisplay`（显示当前的 logical volume）

![img](https://funtl.com/assets/Lusifer_201810310002.png)

**备注：** 注意这里目前有两个，一个是文件系统所在的 `volume`，另一个是 `swap` 分区使用的 `volume`，当然，我们需要扩容的是第一个

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#vgdisplay（显示当前的-volume-group）)`vgdisplay`（显示当前的 volume group）

![img](https://funtl.com/assets/Lusifer_201810310003.png)

**备注：** 注意 `VG SIZE`，这里应该是你当前的可用空间大小，待扩容完毕，这里显示的应该是最终的大小

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#pvdisplay（显示当前的-physical-volume）)`pvdisplay`（显示当前的 physical volume）

![img](https://funtl.com/assets/Lusifer_201810310004.png)

## [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#开始-lvm-扩容)开始 LVM 扩容

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#查看-fdisk)查看 fdisk

```text
fdisk -l
```

![img](https://funtl.com/assets/Lusifer_201810310005.png)

因为这台机器默认开启了 LVM，所以目前有一个 `extended` 分区和一个 `LVM` 分区，并且他们是完全重叠的。这是因为，LVM 分区作为一个虚拟的分区，完全占用了这个 extended 分区，原理图见下：

![img](https://funtl.com/assets/Bg7zYac6&690.png)

因此，现在需要做的就是将 extended partition (`sda2`) 扩展到最大，然后创建一个新的 LVM logical partition (`sda6`)，用它来填满 sda2

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#查看所有连接到电脑上的储存设备)查看所有连接到电脑上的储存设备

```text
fdisk -l |grep '/dev'
```

#### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#_1-块磁盘效果图)1 块磁盘效果图

![img](https://funtl.com/assets/Lusifer_201810310006.png)

#### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#_2-块磁盘效果图（新增磁盘，尚未挂载）)2 块磁盘效果图（新增磁盘，尚未挂载）

![img](https://funtl.com/assets/Lusifer_201810310007.png)

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#创建-sdb-分区)创建 `sdb` 分区

```text
fdisk /dev/sdb
n	# 新建分区
l	# 选择逻辑分区，如果没有，则首先创建扩展分区（p），然后再添加逻辑分区（硬盘：最多四个分区 P-P-P-P 或 P-P-P-E）
```

![img](https://funtl.com/assets/Lusifer_201810310008.png)

```text
回车
回车
回车
w	# 写入磁盘分区
```

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#格式化磁盘)格式化磁盘

![img](https://funtl.com/assets/Lusifer_201810310009.png)

```text
mkfs -t ext4 /dev/sdb1
```

![img](https://funtl.com/assets/Lusifer_201810310010.png)

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#创建-pv)创建 PV

```text
pvcreate /dev/sdb1
```

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#查看卷组)查看卷组

```text
pvscan
```

![img](https://funtl.com/assets/Lusifer_201810310011.png)

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#扩容-vg)扩容 VG

```text
vgdisplay
```

![img](https://funtl.com/assets/Lusifer_201810310012.png)

```text
vgextend ubuntu-vg /dev/sdb1
```

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#扩容-lv)扩容 LV

![img](https://funtl.com/assets/Lusifer_201810310013.png)

![img](https://funtl.com/assets/Lusifer_201810310014.png)

```text
# 增加指定大小
lvextend -L +30G /dev/ubuntu-vg/root
# 按百分比扩容
lvextend -l +100%FREE /dev/ubuntu-vg/root
```

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#刷新分区)刷新分区

```text
resize2fs /dev/ubuntu-vg/root
```

### [#](https://funtl.com/zh/linux/Linux-LVM-磁盘扩容.html#删除-unknown-device)删除 unknown device

```text
pvscan
vgreduce --removemissing ubuntu-vg
```

**注意：不要卸载扩容的磁盘，可能出现丢失数据或是系统无法启动**

上次更新: 2019-6-12 4:53:43

← [部署应用到生产环境](https://funtl.com/zh/linux/部署应用到生产环境.html)