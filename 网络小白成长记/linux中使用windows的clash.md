# linux中使用windows的clash

release author: ningan123

release time: 2022-08-14



## 环境说明

clash运行在windows下

虚拟机是vmware的nat网络模式，所以使用的是vm8网卡



虚拟机的ip：192.168.56.168

虚拟机的网关：192.168.56.2

windows下vm8网卡的ip：192.168.56.1

![image-20220814164916886](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814164916886.png)



![image-20220814171028887](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814171028887.png)





![image-20220814170848227](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814170848227.png)

![image-20220814171119246](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814171119246.png)





## 操作部署

### 1）clash打开"允许局域网连接"

![image-20220814165037015](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814165037015.png)



鼠标放到”允许局域网连接“的地方，可以看到vm8的ip

这个ip是windows中vm8的网卡ip

![image-20220814165150900](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814165150900.png)



### 2）windows关闭防火墙或者开放7890端口

打开控制面板
选择windows 防火墙
点击左侧的“高级设置”选项
设置入站规则
点击“新建规则”，点选“端口”，单击 “下一步”（注意：入站规则是指别人电脑访问自己电脑；出站规则实质自己电脑访问别人电脑）

选择相应的协议（例如：添加7890端口，选择TCP；特定本地端口处输入7890；）
选择“允许连接”，点击“下一步”；
勾选“域”，“专用”，“公司”，点击“下一步”
输入端口名称，点“完成”即可



### 3）linux机器上配置代理地址及端口

```bash
vim ~/.bashrc

添加如下内容：
export http_proxy=http://192.168.56.1:7890
export https_proxy=http://192.168.56.1:7890

source ~/.bashrc
```





### 4）测试

```bash
# 测试7890端口是否通
telnet 192.168.56.1 7890
```

![image-20220814170200958](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814170200958.png)



```bash
# 测试能否成功访问google
curl www.google.com
```

![image-20220814170442788](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220814170442788.png)





## 参考

[[linux添加代理到clash for windows代理](https://www.cnblogs.com/lyy-blog/p/14923024.html)](https://www.cnblogs.com/lyy-blog/p/14923024.html)

[Win10 开放防火墙中某一端口](https://blog.csdn.net/nihaize0520/article/details/80714108)