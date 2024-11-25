---
title: linux-LVM逻辑卷管理
abbrlink: 427bdc6a
date: 2023-10-19 12:50:58
tags:
  - linux
categories:
  - 服务器相关  
---

# 一、基础
    
一个  分区 对应 一个PV <font color="red">【pvcreate /dev/sdb1 /dev/sdb2 /dev/sdc1 /dev/sdc2】</font>

多个PV 对应 一个VG <font color="red">【 vgcreate vgdata /dev/sdb1 /dev/sdb2 /dev/sdc1 /dev/sdc2】 </font>

一个VG 中可以  分出多个LV <font color="red">【 lvcreate -L 11G -n lv001 vgdata 】</font>
 
<font color="red">查看命令 【pvdisplay vgdisplay lvdisplay】</font>
    
     PV(Physical Volume)：物理卷，处于LVM最底层，可以是物理硬盘或者分区。

     VG(Volume Group)：卷组，建立在PV之上，可以含有一个到多个PV  
     
     LV(Logical Volume)：逻辑卷，建立在VG之上，相当于原来分区的概念。不过大小可以动态改变。  
     
     
  
     
     创建PV   pvcreate /dev/sdb1
     
     查看PV   pvscan
     
     创建一个名为vgdata的VG，并且将所有的新建的PV加入vgdata
     vgcreate vgdata /dev/sdb1 
     
     从VG vgdata中创建一个大小为11G的名为lv001的LV
     lvcreate -L 11G -n lv001 vgdata
     
     格式化
     mkfs.ext3    mkfs.ext4    mkfs.xfs
     mkfs.ext4  /dev/mapper/【vg名称】-【lv名称】
     示例mkfs.ext4 /dev/mapper/vgdata-lv001
     
     挂载
     mount /dev/mapper/vgdata-lv001 /data
     
     卸载
      umount 【设备名】
      
     xfs格式不支持缩容，只支持扩容
     
# 二、新硬盘 扩容 根目录 /

## lsblk查看

         NAME               MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
         sda                  8:0    0   60G  0 disk 
         ├─sda1               8:1    0    1G  0 part /boot
         └─sda2               8:2    0   59G  0 part 
           ├─openeuler-root 253:0    0 40.4G  0 lvm  /
           ├─openeuler-swap 253:1    0    6G  0 lvm  
           └─openeuler-home 253:2    0 17.4G  0 lvm  /home
         sdb                  8:16   0    5G  0 disk 
         ├─sdb1               8:17   0  1.9G  0 part 
         └─sdb2               8:18   0  3.1G  0 part 
           └─vgdata-lv001   253:3    0    8G  0 lvm  
         sdc                  8:32   0    5G  0 disk 
         ├─sdc1               8:33   0  2.5G  0 part 
         │ └─vgdata-lv001   253:3    0    8G  0 lvm  
         └─sdc2               8:34   0  2.5G  0 part 
           └─vgdata-lv001   253:3    0    8G  0 lvm  
         sdd                  8:48   0    5G  0 disk 
         └─sdd1               8:49   0    5G  0 part 
           └─openeuler-root 253:0    0 40.4G  0 lvm  /
         sr0                 11:0    1  3.5G  0 rom

    
    
    
## 分区新的硬盘

    fdisk /dev/sdd
    默认值 回车 就可以了
    
## 查看vg
   
    vgscan      
    
    [root@k8snode-2 ~]# vgscan
    Found volume group "vgdata" using metadata type lvm2
    Found volume group "openeuler" using metadata type lvm2
    
    vgdisplay  查看vg还有多少空余空间
    
##  扩展 vg
    
    #将新建的分区/dev/sdd1  加入openeuler vg中
    vgextend openeuler /dev/sdd1   

##  扩充逻辑卷
    
    lvextend -L +5G /dev/mapper/openeuler-root
    
    df -h 可以查看目录挂载的 逻辑卷
    
    [root@k8snode-2 ~]# df -h
    文件系统                    容量  已用  可用 已用% 挂载点
    devtmpfs                    4.0M     0  4.0M    0% /dev
    tmpfs                       3.7G     0  3.7G    0% /dev/shm
    tmpfs                       1.5G  147M  1.4G   10% /run
    tmpfs                       4.0M     0  4.0M    0% /sys/fs/cgroup
    /dev/mapper/openeuler-root   40G  5.0G   33G   14% /
    tmpfs                       3.7G     0  3.7G    0% /tmp
    /dev/sda1                   974M  151M  756M   17% /boot
    /dev/mapper/openeuler-home   17G  709M   16G    5% /home
    
      
## 更新文件系统（争对不同的文件系统，其更新的命令也不一样   

    e2fsck -f /dev/datavg/lvdata1 #ext4文件系统，检查lv的文件系统
    
    resize2fs /dev/VG/LV01 #ext4文件系统命令，该命令后面接lv的设备名就行
    
    xfs_growfs /nas #xfs文件系统，该命令后面直接跟的是挂载点
    
    当更新文件系统后，你就会发现，df -h正常了
    
# 删除其他逻辑券扩容到根目录

         lvremove /dev/centos/home
         lvextend -L +101.12G /dev/centos/root
         xfs_growfs  /dev/centos/root