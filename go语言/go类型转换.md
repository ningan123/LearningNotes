# go语言类型转换

release author: ningan123

release time: 2022-08-17



## 字符串转化为uinit16

```go
package main

import (
	"fmt"
	"strconv"
	"strings"
)

func main() {
	// 方法1
	port1, err := strconv.Atoi("12345")
	if err != nil {
		panic(err)
	}
	port2 := uint16(port1)
	fmt.Printf("type:%T  value:%v\n", port1, port1) // type:int  value:12345
	fmt.Printf("type:%T  value:%v\n", port2, port2) // type:uint16  value:12345
	fmt.Println(port2)                              // 12345

	// 方法2
	port3, err := strconv.ParseUint("1234", 10, 16)
	if err != nil {
		panic(err)
	}
	port4 := uint16(port3)
	fmt.Printf("type:%T  value:%v\n", port3, port3) // type:uint64  value:1234
	fmt.Printf("type:%T  value:%v\n", port4, port4) // type:uint16  value:1234
	fmt.Println(port4)                              // 1234
}

```

