

[TOC]

# ubuntu20.04设置静态ip

release author: ningan123

release time: 2022-07-16



## 需求

问题：ssh远程连接的时候，发现连不上虚拟机。后来进到虚拟机里才看到是因为ip地址变了，所以想改成静态ip。





## 说明

自 17.10 开始，Ubuntu 已放弃在 /etc/network/interfaces 里设置静态 IP 的办法了，即使配置也不会生效，而是改成 netplan 方式 ，配置写在 /etc/netplan/01-network-manager-all.yaml 或者类似名称的 yaml 文件里

Ubuntu20配置值静态ip时需要修改/etc/netplan下面01-network-manager-all.yaml这个文件。

![image-20220716164159864](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716164159864.png)





## 流程

### 查看原始ip地址

![image-20220716165308432](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716165308432.png)







### 要修改/etc/netplan下面01-network-manager-all.yaml这个文件

```bash

[root@ningan ~]#
[root@ningan ~]# cat /etc/netplan/01-network-manager-all.yaml
# Let NetworkManager manage all devices on this system
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    eth0:                           # 配置网卡的名称,通过 ip a 命令查看
      dhcp4: false                    # 关闭 DHCP，如果需要打开 DHCP 则写 true
      addresses: [172.24.48.67/20]    # 配置的静态 IP 地址和掩码
      optional: true
      gateway4: 172.24.48.1            # 网关地址
      nameservers:
        addresses: [8.8.8.8,114.114.114.114]       # DNS 服务器地址，多个 DNS 服务器地址需要用英文逗号分隔开
[root@ningan ~]#

```



网关地址如何确定呢？

我用的是hyper-v启动的虚拟机，

![image-20220716165746966](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716165746966.png)

![image-20220716165911473](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716165911473.png)







### 重启网络

```bash
[root@ningan ~]# netplan apply
```



### 查看网卡设置是否生效

如下：

![image-20220716165204632](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716165204632.png)

已经生效！





### 测试网络连通性

![image-20220716165432286](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716165432286.png)



## 参考

[Ubuntu 20.10设置静态IP地址](https://blog.csdn.net/weixin_56752399/article/details/115564741?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-115564741-blog-122279914.pc_relevant_sortByStrongTime&spm=1001.2101.3001.4242.2&utm_relevant_index=4)
[Hyper-V 和Ubuntu Server 16.04 配置静态IP](https://blog.csdn.net/vincent_duan/article/details/120145526)

