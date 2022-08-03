## 修改终端命令提示符颜色

```bash
vim ~/.bashrc
PS1='\[\e[32;40m\][\u@\h \W]\$\[\e[0m\] '  # green
PS1='\[\e[34;40m\][\u@\h \W]\$\[\e[0m\] '  #blue
source ~/.bashrc
```



## 参考

[(安安)Xshell Linux centOS命令提示符改颜色](https://blog.csdn.net/weixin_42072280/article/details/112536544?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165948630716782248568035%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=165948630716782248568035&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-2-112536544-null-null.nonecase&utm_term=%E7%BB%88%E7%AB%AF&spm=1018.2226.3001.4450)