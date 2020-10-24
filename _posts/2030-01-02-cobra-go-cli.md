---
layout: post
title: "Go编写CLI命令行工具"
date: 2030-01-02 16:30 +0800
categories: tech
---

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
ray@ray-pc:~/go_workspace/src/ahezime.com/ahezime/lab/cli/hello$ cobra init --pkg-name ahezime.com/ahezime/lab/cli/hello
Your Cobra application is ready at
/home/ray/go_workspace/src/ahezime.com/ahezime/lab/cli/hello
```

添加命令: 
```
ray@ray-pc:~/go_workspace/src/ahezime.com/ahezime/lab/cli/hello$ cobra add status
status created at /home/ray/go_workspace/src/ahezime.com/ahezime/lab/cli/hello
```

查看目录结构: 
```
ray@ray-pc:~/go_workspace/src/ahezime.com/ahezime/lab/cli/hello$ tree .
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
ray@ray-pc:~/go_workspace/src/ahezime.com/ahezime/lab/cli/hello$ ./hello version
v0.1
```
