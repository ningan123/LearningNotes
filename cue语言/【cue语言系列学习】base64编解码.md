# 【cue语言系列学习】base64编解码

release author: ningan123

release time: 2022-08-14

## 在线运行

[工具](https://cuelang.org/play/#cue@export@cue)

三种不同的输出方式，输出的结果是不一样的



![image-20220812151050020](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220812151050020.png)



![image-20220812151109172](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220812151109172.png)



![image-20220812151149851](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220812151149851.png)



## 本地模拟

base64.cue

```bash
[root@master base64]# cat base64.cue
import "encoding/base64"

t1: base64.Encode(null, "foo")
t2: base64.Decode(null, base64.Encode(null, "foo"))

```



```bash
[root@master cuePractice]# cue version
cue version v0.4.3 linux/amd64
[root@master cuePractice]# 
[root@master cuePractice]# cue export base64/base64.cue
{
    "t1": "Zm9v",
    "t2": "Zm9v"
}
[root@master cuePractice]# cue export base64/base64.cue --out json
{
    "t1": "Zm9v",
    "t2": "Zm9v"
}
[root@master cuePractice]# cue export base64/base64.cue --out yaml
t1: Zm9v
t2: !!binary Zm9v
[root@master cuePractice]#
```



```bash
[root@master cuePractice]# go run base64/base64.go
scheme: 
{
        t1: "Zm9v"
        t2: 'foo'
}
```



![image-20220812150805711](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220812150805711.png)





```go
// base64.go
package main

import (
	"errors"
	"fmt"
	"os"
	"path/filepath"

	"cuelang.org/go/cue"
	"cuelang.org/go/cue/cuecontext"
	cueerror "cuelang.org/go/cue/errors"
)

func main() {

	ctx := cuecontext.New()

	clusterVal := ctx.NewList()

	confPath := "/root/cuePractice/base64"
	fileData, err := os.ReadFile(filepath.Join(confPath, "base64.cue"))
	if err != nil {
		fmt.Println(err.Error())
	}

	scheme := ctx.CompileBytes(fileData, cue.Scope(clusterVal))
	if scheme.Err() != nil {
		msg := cueerror.Details(scheme.Err(), nil)
		fmt.Println(errors.New(msg))
	}
	fmt.Printf("scheme: \n%s\n", scheme)
}

```



![image-20220812151403446](https://cdn.jsdelivr.net/gh/ningan123/PicGo_Images@main/img/image-20220812151403446.png)
