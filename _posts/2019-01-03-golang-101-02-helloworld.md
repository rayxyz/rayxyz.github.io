---
layout: post
title: "Golang 101 #2 Hello world!"
date: 2019-01-04 13:00 +0800
categories: tech
---

# 在src创建helloworld文件夹
```
cd ~/go_workspace/src
mkdir helloworld
```

# 创建hello.go文件
```
touch hello.go
```

# 编辑hello.go
```
sudo vim hello.go
```

# hello.go
```
pakcage main

import (
        "fmt"
)

func main() {
        fmt.Println("Hello world!")
}
```
[![](/assets/images/go101/no2-helloworld-editing.png)](/assets/images/go101/no2-helloworld-editing.png)

# 编译 & 运行
```
go build -o helloworld
```
[![](/assets/images/go101/no2-run-helloworld.png)](/assets/images/go101/no2-run-helloworld.png)

到此，我们已经开发了Hello world程序，下一篇将介绍Go的目录结构和基本的语法。