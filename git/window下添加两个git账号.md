[TOC]





# window下添加两个git账号

release author: ningan123

release time: 2022-07-16



## 需求

本地电脑的git文件邮箱是用公司的邮箱。想在github上面上传自己的东西，不想用公司的邮箱，想换成自己的邮箱。

本质问题：提交代码的时候，邮箱显示的是公司的邮箱

初步想法：整两个git账号   即为下面的内容

整着整着，发现，可能不需要两个git账号，只要改一下github仓库的用户名和邮箱就可以了。懒得验证了，先这样用吧！





## 步骤

### git 1

```
$ ssh-keygen -t rsa -C "user1@email.com"
```

由于电脑上已经有第一个git账号了，所以这一步就省略了。

### git 2

```
$ ssh-keygen -t rsa -f ~/.ssh/id_rsa2 -C "xxx@xxx.com"
```



登录GitHub，进入【Settings】-【SSH and GPG keys】

点击【New SSH key】按钮，进入新建SSH key页面

将本地<font color=red>公钥</font>内容添加进去

添加完成后在【Git Bash】中输入以下命令SSH密钥是否生效

```

$ ssh -T git@github.com -i ~/.ssh/id_rsa2
```



![image-20220716172112456](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716172112456.png)

成功验证！



### sourcetree拉取github仓库的内容

添加刚刚生成的id_rsa2的密钥



![image-20220716172615986](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716172615986.png)



拉取代码

<font color=red>注意：用ssh的方式拉取</font>



![image-20220716172748466](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716172748466.png)

成功拉取代码！



### sourcetree提交 邮箱修改

提交的时候，发现下面的邮箱为公司邮箱，想换一个邮箱提交改怎么办？

![image-20220716174636334](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220716174636334.png)



方法1：点击上面的”使用另一个作者“，但这种做法是一次性的，下次提交又变回了默认作者

方法2：修改默认作者：在该仓库下设置单独的用户名和密码

```sh
# 1.进入到需要修改的仓库中
git config user.name GitHub的用户名
git config user.email GitHub的登录邮箱
```

改完以后，发现默认作者就已经变成了刚刚修改的邮箱

成功！



## 常用命令

```
#全局配置账户已经移除
git config --global --unset user.name
#查看全局用户名
git config --global user.name
#全局配置邮箱已经移除
git config --global --unset user.email
#查看全局邮箱
git config --global user.email
```



## 参考

[配置多个Git账号（windows 10）](https://blog.csdn.net/q13554515812/article/details/83506172)