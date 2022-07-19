

[TOC]



# 利用k8s的nginx搭建文件下载器

release author: ningan123

release time: 2022-07-19



## 需求

想通过k8s的nginx容器下载主机上的某个文件

方法：通过容器挂载文件挂载的方式，把主机文件下载目录挂载到nginx容器内，并且挂载主机上的nginx.conf文件到nginx容器内。当访问nginx时，会列出相应的文件进行下载即可



## 操作流程

### 主机文件结构：nginx.conf文件 download文件夹



```bash
[root@master nginx]# pwd
/root/nginx
[root@master nginx]# tree
.
├── download
│   └── test.txt
└── nginx.conf

1 directory, 2 files
[root@master nginx]# cat nginx.conf
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;
events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    default_type        application/octet-stream;
    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
            root    /usr/share/nginx/html/download;
        autoindex on;    #开启索引功能
        autoindex_exact_size off;  #关闭计算文件确切大小（单位bytes），只显示大概大小（单位kb、mb、gb）
        autoindex_localtime on;   #显示本机时间而非 GMT 时间
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

[root@master nginx]# cat download/test.txt
Hello, anan
[root@master nginx]#

```



![image-20220719115853392](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220719115853392.png)



### k8s nginx yaml文件

```bash
[root@master nginx]# cat nginx4.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20.1
        ports:
        - containerPort: 80
        volumeMounts:
          - name: conf
            mountPath: /etc/nginx/nginx.conf
            subPath: nginx.conf
          - name: download
            mountPath: /usr/share/nginx/html/download
      volumes:
        - name: conf
          hostPath:
            path: /root/nginx/
        - name: download
          hostPath:
            path: /root/nginx/download/
---
apiVersion: v1
kind: Service
metadata:
  name: ngx-service
  labels:
    app: nginx
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    nodePort: 8899


```

![image-20220719120057356](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220719120057356.png)

```
kubectl apply -f nginx.conf
```

pod成功启动后，访问nodeport端口，可以看到下载列表，进行下载即可！





![image-20220719120130869](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220719120130869.png)



## 参考

[使用docker搭建nginx文件下载服务器](https://blog.csdn.net/qq_39218530/article/details/108022907)