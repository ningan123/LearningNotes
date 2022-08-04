# Windows下如何使用VScode连接远程linux服务器进行远程开发

release author: ningan123

release time: 2022-05-05



## 1. 先上手-成功连接

### 1.vscode下载安装所需插件：vscode中的remote-ssh插件



![img](https://img-blog.csdnimg.cn/3b127ff942f64d9cba9a926ae897847c.png)



安装之后，就会出现上图黄色所示的图标



### 2.点击远程图标中的配置按钮，选择config文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/88b2ecaacca4469cad2f1c0ed547f6d3.png)



### 3.配置config文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/97880d2fc46749dd9f00248e88db9854.png)



Host部分输入服务器的外号（如“test”）
HostName部分输入服务器的地址
User部分输入用户的名称

config文件中写的Host名称test就会显示在最左侧。

![在这里插入图片描述](https://img-blog.csdnimg.cn/31e20e20cd04429b8d232bffb40b9c61.png)
### 4.新建窗口连接
此时，单击“新建连接”按钮，vscode会重新打开一个窗口，提示输入远程服务器的密码。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9759c53f6ac440c5b8849dd5907d0b11.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/fa51d90355ce49f19f5c46ff09d3ab71.png)





## 2. 进阶版-免密

上面的方式每次连接主机都需要输入密码，为了简化操作，使用公钥进行免密登录。
```bash
linux服务器：
[root@test ~]$ mkdir .ssh
[root@test ~]$ cd .ssh
[root@test .ssh]$ vi authorized_keys
#此处输入windows的.../.ssh/id_rsa.pub内容
[root@test .ssh]$ chmod 600 authorized_keys

windows下的vscode：
直接点击新建窗口，即可成功免密跳转！
```