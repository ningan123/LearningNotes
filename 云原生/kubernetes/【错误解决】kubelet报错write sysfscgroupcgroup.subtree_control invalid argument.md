# 【错误解决】kubelet报错write sysfscgroupcgroup.s        ubtree_control invalid argument

release author: ningan123

release time: 2022-07-26



## 报错信息

### 主要报错信息

```bash
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.712234 3452279 cgroup_manager_linux.go:603] cgroup update failed failed to enable controllers on "/sys/fs/cgroup": write /sys/fs/cgroup/cgroup.s        ubtree_control: invalid argument
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.712292 3452279 kubelet.go:1384] "Failed to start ContainerManager" err="failed to initialize top level QOS containers: root container [kubepods]         doesn't exist"
```





### 详细报错信息

```bash
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.525681 3452279 clientconn.go:948] ClientConn switching balancer to "pick_first"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.525754 3452279 balancer_conn_wrappers.go:78] pickfirstBalancer: HandleSubConnStateChange: 0xc0009f15b0, {CONNECTING <nil>}
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.525999 3452279 balancer_conn_wrappers.go:78] pickfirstBalancer: HandleSubConnStateChange: 0xc0009f15b0, {READY <nil>}
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.526307 3452279 factory.go:137] Registering containerd factory
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.526350 3452279 factory.go:101] Registering Raw factory
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.526382 3452279 manager.go:1207] Started watching for new ooms in manager
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.526643 3452279 manager.go:301] Starting recovery of all containers
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.544217 3452279 kubelet_network_linux.go:56] "Initialized protocol iptables rules." protocol=IPv4
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.546438 3452279 manager.go:306] Recovery completed
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.559004 3452279 kubelet_network_linux.go:56] "Initialized protocol iptables rules." protocol=IPv6
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.559021 3452279 status_manager.go:157] "Starting to sync pod status with apiserver"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.559037 3452279 kubelet.go:1846] "Starting kubelet main sync loop"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.559088 3452279 kubelet.go:1870] "Skipping pod synchronization" err="[container runtime status check may not have completed yet, PLEG is not heal        thy: pleg has yet to be successful]"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.624882 3452279 kubelet_node_status.go:362] "Setting node annotation to enable volume controller attach/detach"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.624892 3452279 kuberuntime_manager.go:1044] "Updating runtime config through cri with podcidr" CIDR="100.64.64.0/24"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.625313 3452279 kubelet_network.go:76] "Updating Pod CIDR" originalPodCIDR="" newPodCIDR="100.64.64.0/24"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.628688 3452279 kubelet_node_status.go:554] "Recording event message for node" node="node0" event="NodeHasSufficientMemory"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.628716 3452279 kubelet_node_status.go:554] "Recording event message for node" node="node0" event="NodeHasNoDiskPressure"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.628729 3452279 kubelet_node_status.go:554] "Recording event message for node" node="node0" event="NodeHasSufficientPID"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.628759 3452279 kubelet_node_status.go:71] "Attempting to register node" node="node0"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.633321 3452279 kubelet_node_status.go:109] "Node was previously registered" node="node0"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.633380 3452279 kubelet_node_status.go:74] "Successfully registered node" node="node0"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.659182 3452279 kubelet.go:1870] "Skipping pod synchronization" err="container runtime status check may not have completed yet"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659400 3452279 cpu_manager.go:199] "Starting CPU manager" policy="none"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659415 3452279 cpu_manager.go:200] "Reconciling" reconcilePeriod="10s"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659430 3452279 state_mem.go:36] "Initialized new in-memory state store"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659587 3452279 state_mem.go:88] "Updated default CPUSet" cpuSet=""
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659604 3452279 state_mem.go:96] "Updated CPUSet assignments" assignments=map[]
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659613 3452279 state_checkpoint.go:136] "State checkpoint: restored state from checkpoint"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659619 3452279 state_checkpoint.go:137] "State checkpoint: defaultCPUSet" defaultCpuSet=""
Jul 25 15:21:20 n15.dcos kubelet[3452279]: I0725 15:21:20.659626 3452279 policy_none.go:44] "None policy: Start"
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.712234 3452279 cgroup_manager_linux.go:603] cgroup update failed failed to enable controllers on "/sys/fs/cgroup": write /sys/fs/cgroup/cgroup.s        ubtree_control: invalid argument
Jul 25 15:21:20 n15.dcos kubelet[3452279]: E0725 15:21:20.712292 3452279 kubelet.go:1384] "Failed to start ContainerManager" err="failed to initialize top level QOS containers: root container [kubepods]         doesn't exist"
Jul 25 15:21:20 n15.dcos systemd[1]: kubelet.service: Main process exited, code=exited, status=1/FAILURE
Jul 25 15:21:20 n15.dcos systemd[1]: kubelet.service: Failed with result 'exit-code'.
-- Subject: Unit failed
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- The unit kubelet.service has entered the 'failed' state with result 'exit-code'.
Jul 25 15:21:20 n15.dcos systemd[1]: kubelet.service: Consumed 766ms CPU time
-- Subject: Resources consumed by unit runtime
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- The unit kubelet.service completed and consumed the indicated resources.

```



## 环境记录



```bash
[root@n15 ~]# cd /sys/fs/cgroup
[root@n15 cgroup]# ll
total 0
-r--r--r--   1 root root 0 Jul 25 14:11 cgroup.controllers
-rw-r--r--   1 root root 0 Jul 25 14:11 cgroup.max.depth
-rw-r--r--   1 root root 0 Jul 25 14:11 cgroup.max.descendants
-rw-r--r--   1 root root 0 Jul 25 14:11 cgroup.procs
-r--r--r--   1 root root 0 Jul 25 14:11 cgroup.stat
-rw-r--r--   1 root root 0 Jul 25 15:04 cgroup.subtree_control
-rw-r--r--   1 root root 0 Jul 25 14:11 cgroup.threads
drwxr-xr-x   2 root root 0 Jul 25 15:04 init.scope
drwxr-xr-x   4 root root 0 Jul 25 15:04 kubepods.slice
drwxr-xr-x   2 root root 0 Jul 25 15:04 machine.slice
-rw-r--r--   1 root root 0 Jul 25 14:11 memory.use_priority_oom
drwxr-xr-x 129 root root 0 Jul 25 15:04 system.slice
drwxr-xr-x   4 root root 0 Jul 25 15:04 user.slice
[root@n15 cgroup]#

```



## 问题说明

看到kubelet的报错，想到之前主机做cgroupv2测试，更改了这个参数





## 问题解决

将cgroupv2改回来就ok了

1）启动参数去掉 systemd.unified_cgroup_hierarchy=1 

改动前：

```bash
[root@n15 bin]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=false
GRUB_TERMINAL_OUTPUT="gfxterm"
GRUB_CMDLINE_LINUX="crashkernel=512M resume=/dev/mapper/rootvg-lv_swap rd.lvm.lv=rootvg/lv_root rd.lvm.lv=rootvg/lv_swap rhgb quiet cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory systemd.unified_cgroup_hierarchy=1"
GRUB_DISABLE_RECOVERY="true"
GRUB_ENABLE_BLSCFG=true

```

改动后：

```bash
[root@n15 bin]# cat /etc/default/grub
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=false
GRUB_TERMINAL_OUTPUT="gfxterm"
GRUB_CMDLINE_LINUX="crashkernel=512M resume=/dev/mapper/rootvg-lv_swap rd.lvm.lv=rootvg/lv_root rd.lvm.lv=rootvg/lv_swap rhgb quiet cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory"
GRUB_DISABLE_RECOVERY="true"
GRUB_ENABLE_BLSCFG=true

```



2）更新下grub  grub2-mkconfig -o /boot/grub2/grub.cfg 

```bash
[root@n15 bin]# grub2-mkconfig -o /boot/grub2/grub.cfg 
Generating grub configuration file ...
done
```

3） 重启主机

```
reboot
```



