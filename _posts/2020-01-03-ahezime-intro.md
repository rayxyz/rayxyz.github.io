---
layout: post
title: "Ahezime Introduction"
date: 2020-01-03 16:30 +0800
categories: tech
---

# 关于 Ahezime
Ahezime.com是一个用户可以在上面写博客和发帖分享知识、传播正能量的博客和社区平台网站。
平台前端基于响应式布局设计开发，能同时自适应PC端和移动端。

PC首页显示：

![https://rayxyz.github.io/assets/images/ahezime/ahezime-index.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-index.png)

#### 移动设备首页显示
![https://rayxyz.github.io/assets/images/ahezime/ahezime-index-mobile-not-log-in.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-index-mobile-not-log-in.png)

# 注册 & 登录
为了能为用户便于使用平台和带来较好的体验，在使用平台前要先注册成为平台的用户，注册用户只需要一个您经常使用的有效电子邮箱。

[马上注册->](https://ahezime.com/register)

[快速登录->](https://ahezime.com/login)

[快速访问->](https://ahezime.com)


#### 注册
![https://rayxyz.github.io/assets/images/ahezime/ahezime-register.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-register.png)

#### 登录
![https://rayxyz.github.io/assets/images/ahezime/ahezime-login.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-login.png)


# 平台

## 平台架构

#### 总体架构
![https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch.png)

平台系统在一开始就基于微服务(Microservice)架构开发，将传统的单一系统解耦成多个服务，每个服务负责实现平台中特定的功能；并且，每个服务可以启动多个实例，为平台的稳定、高性能运行提供最基础的保障。

#### 详细架构
![https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch-details.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch-details.png)

平台由Nginx作为暴露在网络上的前端、平台API Gateway(API网关)、服务注册和服务发现中心（Agenble）、服务这几个大块组成。

###### Nginx
Nginx负责静态内容资源文件服务和整合HTTPS保护内容网络传输安全。

###### 服务注册和服务发现中心Agenble
Agenble是自主开发的微服务注册和服务发现中心中间件。当服务进程启动后会主动注册到Agenble，Agenble在服务注册完成后会发送TCP连接请求来做服务健康检查（Health Check），当检测到服务不在线时，主动移除注册的服务，保证服务的可用性。已注册服务也会主动向Agenble发送心跳，当服务被Agenble移除后，Agenble会根据服务的心跳来确定是否注册服务。这样，Agenble和服务间的双向互动在最大程度上保证服务的高可用性。

###### 平台API网关
负责反向代理（Reverse Proxy）、负载均衡（LB: LoadBalancing）、rate limiting、权限验证和API统计分析等。

API网关提供反向代理和负载均衡的服务——API网关定时请求Agenble服务注册中心，拉取已注册服务的实时信息，当得知注册中心的服务发生变更时，及时更新本地缓存的服务信息，并根据服务发送给注册中的心跳信息，找到最优的服务为下次客户端请求服务，高效地响应客户端的请求。

## 平台状态

#### 运行的服务状态
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-devops-service-status.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-devops-service-status.png)

#### 运行的服务实例的实时状态
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-devops-service-status-service-instance-status.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-devops-service-status-service-instance-status.png)

Ahezime.com分前端网站和后端平台两部分。前端网站当前主要提供博客文章写作、社区互动和其他简单的信息查询。 后端平台主要提供用户的基本信息查看、修改和相应服务的复杂设置、分析，根据用户角色权限的不同决定用户可用的功能。后端平台尽量使用当前流行的新技术，使平台具备数据可视化，对繁杂的数据综合分析，进而做到化繁为简。

#### 服务进程(服务实例)管理
为了实现服务进程生命周期的完全控制，自主研发ahzprocess服务进程管理工具。

* 服务进程管理工具系统服务状态
![https://rayxyz.github.io/assets/images/ahezime/ahezime-ahzprocess-system-service-status.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-ahzprocess-system-service-status.png)

* 查看服务进程状态
![https://rayxyz.github.io/assets/images/ahezime/ahezime-ahzprocessctl-service-instances-status.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-ahzprocessctl-service-instances-status.png)

> 代码示例

``` go
// Start service process :
func (ps *ProcessService) start(svc *ProcessService) (*os.Process, error) {
	ps.Lock()
	defer ps.Unlock()

	logFile, err := os.OpenFile(svc.LogFilePath, os.O_WRONLY|os.O_CREATE|os.O_APPEND, 0666)
	if err != nil {
		log.Fatal(err)
	}
	logErrFile, err := os.OpenFile(svc.LogErrFilePath, os.O_WRONLY|os.O_CREATE|os.O_APPEND, 0666)
	if err != nil {
		log.Fatal(err)
	}

	svc.LogFile = logFile // put log file to process service
	svc.LogErrFile = logErrFile

	procAttr := &os.ProcAttr{
		// Files: []*os.File{os.Stdin, os.Stdout, os.Stderr},
		Files: []*os.File{nil, svc.LogFile, svc.LogErrFile},
		Sys: &syscall.SysProcAttr{
			Setpgid: true,
		},
	}

	ip := network.GetOutboundIP().String()
	// We obtain network port here to avoid port resource race condition.
	port, err := network.ObtainAvailablePort(20000, 30000)
	if err != nil {
		log.Errorf("start process error, service name => %s, exiting...", svc.Name)
		syscall.Kill(os.Getpid(), syscall.SIGKILL)
	}
	args := []string{svc.BinPath, svc.Name, ip, strconv.Itoa(port)}
	log.Infof("start process of service %s", svc.Name)
	proc, err := os.StartProcess(svc.BinPath, args, procAttr)
	if err != nil {
		log.Error(err)
		svc.LogFile.Close()
		svc.LogErrFile.Close()
		if proc != nil {
			proc.Kill()
			return nil, err
		}
	}
	svc.Process = proc
	svc.StartTime = time.Now()
	serviceMap[svc.Name] = svc

	log.Info("process started")

	return proc, nil
}
```

#### Dashboard
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-dashboard.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-dashboard.png)

#### 服务器状态监控
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-hardware-status-monistoring-local.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-hardware-status-monistoring-local.png)

对服务器做了基本的硬件实时可视化状态监控，让管理员能实时查看服务器的运行状态，及时对紧急情况做出响应。

## 平台安全

#### API访问频率控制
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-rate-limiting.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-rate-limiting.png)

除了基本的状态监控，对于API攻击做了基本的访问频率控制，当用户恶意访问平台相关API时，如果超过设定的阀值，用户访问将会受到限制。


## 进入平台
![https://rayxyz.github.io/assets/images/ahezime/ahezime-right-top-hover-menu.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-right-top-hover-menu.png)

如上图，登录后将鼠标悬停在首页的右上角用户头像上，弹出菜单，点击`平台`即可。

## 平台首页
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-index.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-index.png)

平台首页展示当前登录用户的基本信息和用户平台使用的关键信息。

## 修改个人信息
![https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-edit-user-info.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-platform-edit-user-info.png)

用户可以到`设置->用户设置`来更新或完善用户信息。

# 博客
## 博客首页
![https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-index-page.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-index-page.png)


#### 移动端显示效果
![https://rayxyz.github.io/assets/images/ahezime/ahezime-post-index-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-post-index-mobile.png)


## 博客文章详情
![https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-details-page.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-details-page.png)

![https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-details-page-comments-section.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-details-page-comments-section.png)


#### 移动端显示效果
![https://rayxyz.github.io/assets/images/ahezime/ahezime-post-details-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-post-details-mobile.png)

![https://rayxyz.github.io/assets/images/ahezime/ahezime-post-details-comments-section-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-post-details-comments-section-mobile.png)


## 写文章

用户可以自由在平台上写博客文章，文章内容为markdown格式，如果堆markdown格式不熟悉，可参考

英文 [https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

中文 [https://www.markdown.cn/](https://www.markdown.cn/)

![https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-post-edit-show-settings.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-post-edit-show-settings.png)

文章写作界面（显示相关设置）。

![https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-post-edit-not-showing-settings.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-blog-post-edit-not-showing-settings.png)

文章写作界面（隐藏相关设置）。


# 社区
社区为平台上用户间互动的最重要纽带，用户可以分享任何（法律范围内）想要分享的新闻、知识、经验等等。用户可以点赞、收藏、转发、评论社区帖子和回复其他用户。

![https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-page.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-page.png)


#### 移动端显示效果
![https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-mobile.png)

![https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-loadmore-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-index-loadmore-mobile.png)

## 帖子详情
![https://rayxyz.github.io/assets/images/ahezimeahezime-community-post-details.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-post-details.png)

![https://rayxyz.github.io/assets/images/ahezime-community-post-details-page-comments-section.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-post-details-page-comments-section.png)


## 发帖
![https://rayxyz.github.io/assets/images/ahezime-community-post-edit.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-community-post-edit.png)


#### 移动端显示效果 
![https://rayxyz.github.io/assets/images/ahezime-post-community-post-mobile.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-post-community-post-mobile.png)

