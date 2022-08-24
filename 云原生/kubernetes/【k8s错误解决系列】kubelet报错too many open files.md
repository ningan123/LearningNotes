# 【k8s错误解决系列】kubelet报错too many open files.md

release author: ningan123

release time: 2022-08-12





## 报错日志



```bash
# kubelet错误日志
8月 12 15:15:22 n10.dcos kubelet[3638030]: E0812 15:15:22.443686 3638030 kuberuntime_manager.go:790] "CreatePodSandbox for pod failed" err="rpc error: code = Unknown desc = failed to create containerd task: failed to start shim: start failed: : fork/exec /usr/local/bin/containerd-shim-runc-v2: too many open files: unknown" pod="default/rs11-69f5d8d4d4-29jdp"


# containerd错误日志
8月 12 15:34:59 n10.dcos containerd[3637923]: time="2022-08-12T15:34:59.970076202+08:00" level=error msg="StartContainer for \"901e8d0877bb66a80794e4990ac7e31af808451e0eca9b9246c59b1b46d700f4\" failed" error="failed to create containerd task: failed to start shim: open /run/containerd/io.containerd.runtime.v2.task/k8s.io/901e8d0877bb66a80794e4990ac7e31af808451e0eca9b9246c59b1b46d700f4/config.json: too many open files: unknown"
```



## 解决流程

```bash
# 找到当前进程的pid
[root@n10 proc]# ps -ef |grep containerd
...
root      423863       1 38 15:49 ?        00:00:06 /usr/local/bin/containerd -c=/vdata/tg-paas/cuk/cuixwtest-564033574421-m17n810net0810/cfg/containerdConfig.toml
...

# 根据pid查看该进程当前使用的文件句柄数
[root@n10 proc]# ls /proc/423863/fd | wc -l
1009

# 查看当前进程的句柄数限制
[root@n10 proc]# cat /proc/423863/limits | grep "files"
Max open files            1024                 262144               files

# 修改单个进程的文件句柄数限制
[root@n10 proc]# prlimit --pid 423863 --nofile=4096:262144

# 查看当前进程的句柄数限制
[root@n10 proc]# cat /proc/423863/limits | grep "files"
Max open files            4096                 262144               files


#######
# 系统允许打开的最大文件数
[root@n10 proc]# ulimit -n
1024

# 设置系统最大的文件数为2048
[root@n10 ~]# echo 'ulimit -n 4096'>> /etc/profile
[root@n10 ~]# source /etc/profile
[root@n10 ~]# ulimit -n
4096

```

restart一下进程，再次查看containerd的日志，就已经正常了！







## 参考

[**修改进程使用的文件句柄数** ](https://blog.51cto.com/nicemove/1257961)