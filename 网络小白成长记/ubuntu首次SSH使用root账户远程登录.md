[TOC]

# ubuntu首次SSH使用root账户远程登录

release author: ningan123

release time: 2022-02-16



## 步骤

### 1. 安装ssh
 ubuntu  都原生有了ssh客户端，可以通过ssh 命令检验.
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/0597cc190a9e4defb9cd2edc98458cb4.png)
远程连接的服务器端没有自带，需要自行安装，命令为：

sudo apt install openssh-server


### 2. 检查ssh是否成功启动
```
 /etc/init.d/ssh status
 systemctl status ssh
ps -e | grep sshd
```



### 3. 远程连接
ssh username@ip



-----

## 问题：可以远程连接普通用户(ningan)，但是却不可以远程连接root用户

配置root用户SSH服务

Ubuntu中SSH服务安装完成后查看是否允许root用户登陆，若不允许则无法远程登陆root用户，需要修改配置

打开“/etc/ssh/sshd_config”

sudo vim  /etc/ssh/sshd_config

找到并用#注释掉这行：PermitRootLogin prohibit-password

查看是否有“PermitRootLogin yes”新建一行 添加：PermitRootLogin yes

重启服务

```
 /etc/init.d/ssh restart
 systemctl restart ssh

```

可以成功连接
![在这里插入图片描述](https://img-blog.csdnimg.cn/cd09fe53e108439ca4e13df258ab0972.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5a6J5a6JY3Nkbg==,size_20,color_FFFFFF,t_70,g_se,x_16)


## 参考
参考：https://blog.csdn.net/howiecode/article/details/120457571
