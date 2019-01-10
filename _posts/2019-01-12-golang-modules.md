---
layout: post
title: "Golang依赖管理： Go Modules"
date: 2019-01-10 13:00 +0800
categories: tech
---

# 什么是依赖管理
依赖管理(Dependency Management)，像Java的项目管理工具Maven已经Linux内核的lib版本管理。
包依赖管理工具npm, Maven, Gradle等等。

DM pic here (A->Cv1.0, B->Cv1.1) => (A->Cv1.2, B->Cv1.2) => too new.

# Go依赖管理历史
GOPATH -> vendor -> godep, xxxx -> vgo -> Go Modules -> 官方标准。

# Go 1.11 Modules
以实际例子说明Go Modules
```
ahezime
    ├── framework
    │   ├── exception
    │   │   └── exception.go
    │   └── util
    │       ├── file.go
    │       ├── time.go
    │       └── util.go
    ├── main.go
    ├── README.md
    └── services
        └── auth
            ├── auth
            ├── auth_test.go
            └── main.go
```

## Modules

## go.mod

## Version Selection

## Semantic Import Versioning

# 总结
