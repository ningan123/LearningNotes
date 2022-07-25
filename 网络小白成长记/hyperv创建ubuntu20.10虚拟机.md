

# hyperv创建ubuntu20.10虚拟机

release author: ningan123

release time: 2022-07-26



## 准备工作 镜像下载

[Ubuntu 20.10 (Groovy Gorilla)](https://old-releases.ubuntu.com/releases/20.10/)



## 操作步骤(成功创建)

开始->设置->应用->程序和功能->启用或关闭Windows功能。
在打开的窗口中，选择“Hyper-V”

新建虚拟机，流程如下：



![image-20220725165812170](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725165812170.png)



![image-20220725165856177](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725165856177.png)





![image-20220725165936840](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725165936840.png)



![image-20220725170507477](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725170507477.png)



![image-20220725170541316](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725170541316.png)



![image-20220725170629907](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725170629907.png)



这一块不敢点呀不敢点，就害怕把自己windows的硬盘格式化了

从网上查了好多，都说没事的

终于顶着一颗悬着的心点了下去



![image-20220725170709688](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725170709688.png)





![image-20220725171844768](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725171844768.png)



可以输入“Shanghai”

也可以点击地图中的位置



![image-20220725172016759](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725172016759.png)



点击“ restart”



![image-20220725172923147](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725172923147.png)



点击“enter”



![image-20220725173115871](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725173115871.png)



这个真的是吓死我了



## 优化

默认显示如下：

![image-20220725174238658](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725174238658.png)





### 调整虚拟机的分辨率与性能

默认情况下，Hyper-V下的 Ubuntu 桌面的分辨不够理想，需要我们手动调整，请在 Ubuntu 桌面上右键，在菜单中选择 “Open in Terminal”， 在打开的Terminal终端窗口中输入以下指令：

```
sudo gedit /etc/default/grub
```

此时，系统会打开一个文本编辑器，请在当前文件中寻找到 GRUB_CMDLINE_LINUX_DEFAULT 所在行，在最后加上 video=hyperv_fb:[1920x1080]，如下图所示：

![image-20220725174647281](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725174647281.png)



保存上述文件后退出文本编辑器，在 Terminal 终端窗口中输入以下指令：

```
sudo update-grub
```

最后，请重新启动 Ubuntu 虚拟机，重新登录后的 Ubuntu 桌面，其分辨率将设置为您刚才指定的大小。







## 创建失败

###  失败例子1

![image-20220725135724492](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725135724492.png)





![image-20220725133331911](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725133331911.png)

###  失败例子2

选择第二代

安全启动那块选了微软的那个选项

也报错了，没有成功



![image-20220725133331911](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725133331911.png)



## hyper操作

### 从 Hyper-V 管理器重启服务

1. 打开 Hyper-V 管理器。 单击**“开始”**，指向**“管理工具”**，然后单击**“Hyper-V 管理器”**。
2. 在导航窗格中，单击服务器的名称（如果尚未选择）。
3. 在" **操作"窗格中** ，单击" **启动服务"**。





## ubuntu20.10更换源

Ubuntu 的软件源配置文件是 /etc/apt/sources.list。将系统自带的该文件做个备份，将该文件替换为下面内容，即可使用国内的软件源镜像。
一定要备份，一定要备份，一定要备份，重要的事情说三遍。

1.备份原始源文件source.list
sudo  cp   /etc/apt/sources.list   /etc/apt/sources.list.bak

2.修改源文件sources.list

删除原来的文件内容，复制下面的官方源到其中并保存

 [中科大官网](https://mirrors.ustc.edu.cn/repogen/)

下面是中科大 ubuntu20.10的源

```
deb https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-security main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-security main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-updates main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-updates main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-backports main restricted universe multiverse
deb-src https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-backports main restricted universe multiverse

## Not recommended
# deb https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu-old-releases/ubuntu/ groovy-proposed main restricted universe multiverse
```

3.更新源
桌面终端执行命令：sudo apt update更新软件列表，换源完成。









## 参考



[win10 Hyper-V安装配置ubuntu虚拟机](https://blog.csdn.net/fedragon/article/details/117300451) 重要 参考”操作步骤“

[在 Hyper-V 中安装 Ubuntu 系统](https://www.szdamai.com/help/app/ubuntuhyperv) 次重要 参考”更改分辨率“

[Hyper-V 虚拟机管理服务必须正在运行](https://docs.microsoft.com/zh-cn/windows-server/virtualization/hyper-v/best-practices-analyzer/the-hyper-v-virtual-machine-management-service-must-be-running)

[Ubuntu 20.10 groovy 更换国内源](https://blog.csdn.net/weixin_44302833/article/details/110132032)

[修改Ubuntu操作系统root默认密码](https://cloud.tencent.com/developer/article/1434564)