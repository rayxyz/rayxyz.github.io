---
layout: post
title: "用Cobra编写Go CLI命令行工具"
date: 2030-01-02 16:30 +0800
categories: tech
---

在开发中命令行工具是非常有用的，可以完成大多数自动化工作。由于Go标准库在开发实际CLI应用时往往有很多限制，所以有一个用来简化CLI开发的工具是相当重要的，会为我们节省很多时间和精力。Cobra作为Golang生态系统中比较受欢迎的CLI开发工具，可以自动生成命令，我们不用手动繁琐地添加，省去了大量的时间。

# Cobra
安装: 

Cobra:
```
go get -u github.com/spf13/cobra
```

Cobra代码生成工具: 
```
go get -u github.com/spf13/cobra/cobra
```

如果安装代码生成工具报错:
```
go get github.com/spf13/cobra/cobra
go: github.com/spf13/cobra/cobra upgrade => v0.0.0-20200916152758-7f8e83d9366a
go get github.com/spf13/cobra/cobra: ambiguous import: found package github.com/spf13/cobra/cobra in multiple modules:
	github.com/spf13/cobra v1.1.1 (/home/ray/go_workspace/pkg/mod/github.com/spf13/cobra@v1.1.1/cobra)
	github.com/spf13/cobra/cobra v0.0.0-20200916152758-7f8e83d9366a (/home/ray/go_wo
```

加上响应的版本号解决版本冲突问题: 
```
go get -u github.com/spf13/cobra/cobra@v1.1.1
```

# 使用

官方代码生成器使用说明: [Cobra Generator](https://github.com/spf13/cobra/blob/master/cobra/README.md)

初始化:
```
ray@ray-pc:~/lab/cli/hello$ cobra init --pkg-name ~/lab/cli/hello
Your Cobra application is ready at
/home/ray/lab/cli/hello
```

添加命令: 
```
ray@ray-pc:~/lab/cli/hello$ cobra add status
status created at /home/ray/lab/cli/hello
```

查看目录结构: 
```
ray@ray-pc:~/lab/cli/hello$ tree .
.
├── cmd
│   ├── list.go
│   ├── root.go
│   ├── status.go
│   └── version.go
├── hello
├── LICENSE
└── main.go

1 directory, 7 files
```

修改`version`命令，运行:  
```
ray@ray-pc:~/lab/cli/hello$ ./hello version
v0.1
```
