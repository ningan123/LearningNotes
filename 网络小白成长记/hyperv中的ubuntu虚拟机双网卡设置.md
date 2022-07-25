# hyperv中的ubuntu虚拟机双网卡设置

release author: ningan123

release time: 2022-07-26

## 场景

hyperv创建虚拟机的时候，默认用的是default switch，这个default switch生成的ipv4地址是变化的

## 需求

有一个不变的ipv4地址，方便ssh远程连接

同时还可以连接外网



## 操作

hyperv同时整两个网卡，一个网卡(新建一个内部的网卡)配置静态ip地址，一个网卡(default switch)用于连外网

![image-20220725233717830](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220725233717830.png)



### 主机网卡配置

default switch不做任何更改，使用默认配置，如下：

![image-20220726000455898](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726000455898.png)



新建的内部网卡，配置为自定义的东西

![image-20220726000604540](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726000604540.png)



### 虚拟机网卡配置

![image-20220726000941694](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726000941694.png)



![image-20220726001702691](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726001702691.png)



eth0不做更改

![image-20220726001057558](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726001057558.png)



eth1改成自定义ip地址

为什么此处不写网关呢？因为不需要连接外网



![image-20220726001158509](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726001158509.png)







![image-20220726001316264](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220726001316264.png)



成功用自己加的网卡对应的静态ip地址进行ssh连接，搞定！

此处，感谢小王的指导，比心！ 比♥

