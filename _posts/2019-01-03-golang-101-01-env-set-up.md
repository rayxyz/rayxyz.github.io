---
layout: post
title: "Golang 101 #1 环境搭建"
date: 2019-01-02 13:00 +0800
categories: tech
---

# 说明
本系列教程基于Ubuntu 16.04 LTS操作系统编写。
## Go官网
国际
![golang.org](https://golang.org)
国内
![golang.google.cn](https://golang.google.cn)

# 安装Go
下载地址：https://golang.org/dl/ 或 https://golang.google.cn/dl/

解压文件到 /usr/local
```
sudo tar -xzvf go1.11.4.linux-amd64.tar.gz -C /usr/local
```

# 创建Go的工作空间
```
cd ~
mkdir go_workspace
cd go_workspace
mkdir package
mkdir src
mkdir bin
```

# 环境变量配置
添加Go的path变量和GOPATH
```
sudo gedit /etc/environment
```

完成后的environment内容：
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/usr/local/go/bin"
GOPATH="/home/ray/go_workspace"
```

GOPATH为创建的工作空间的目录路径。
由于/etc/environment系统环境变量，又重启后才生效：
```
reboot
```

# 验证是否安装成功
查看版本信息:
```
go version
```

显示如下说明安装成功：
```
go version go1.11.4 linux/amd64
```
