---
layout: post
title: "Golang gRPC+Protobuf3.x Installation"
date: 2019-01-14 13:00 +0800
categories: tech
---

[![](https://rayxyz.github.io/assets/images/general/grpc-pic.png)](https://rayxyz.github.io/assets/images/general/grpc-pic.png)

# Install gRCP
```
go get -u google.golang.org/grpc
```

# Build & Install Protobuf3.x into the system
Download the release from the offcial website
[https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)

Visit the below guide to build & install gRPC:
[https://rayxyz.github.io/tech/2017/06/16/build-protobuf-v3.0.0-on-ubuntu-linux.html](https://rayxyz.github.io/tech/2017/06/16/build-protobuf-v3.0.0-on-ubuntu-linux.html)

# Install protoc-gen-go from the internet
```
go get -u github.com/golang/protobuf/protoc-gen-go
```
or build from source
### Build protoc-gen-go from source
Download protobuf 
[https://github.com/golang/protobuf](https://github.com/golang/protobuf)
```
cd $GOPATH/src/github.com/golang/protobuf/protoc-gen-go
go build
cp protoc-gen-go $GOPATH/bin
```

# Setup the environment variables & alias to ease the development
```
sudo vim ~/.bash_profile
```
Copy the following code into the ~/.bash_profile file
```
export LD_LIBRARY_PATH=/usr/local/lib
export PATH=$PATH:$GOPATH/bin

alias xprotoc="/usr/local/bin/protoc -I=. --go_out=plugins=grpc:."
```

# Write a proto file 
```
cd $GOPATH/src/jlms/services/auth/proto
touch auth.proto
```

copy the below code into the auth.proto file:
```
syntax = "proto3";

package proto;

service AuthSvc {

}

message Auth {
    int64 id = 1;
}
```

# Try it out
```
source ~/.bash_profile
xprotoc auth.proto
```

The generated Go protobuf file:
[![](https://rayxyz.github.io/assets/images/general/protobuf-generated-file.png)](https://rayxyz.github.io/assets/images/general/protobuf-generated-file.png)

Now, the gRPC & Protobuf have been setup.
