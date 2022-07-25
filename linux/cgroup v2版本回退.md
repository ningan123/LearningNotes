# cgroup v2版本回退

release author: ningan123

release time: 2022-07-26



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

