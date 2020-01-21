---
layout: post
title: "Ahezime平台架构详解"
date: 2020-01-17 23:14 +0800
categories: tech
---

## 前言

Ahezime平台地址: [https://ahezime.com](https://ahezime.com)

## 总体架构

![https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch.png)

平台系统在一开始就基于微服务(Microservice)架构开发，将传统的单一系统解耦成多个服务，每个服务负责实现平台中特定的功能；并且，每个服务可以启动多个实例，为平台的稳定、高性能运行提供最基础的保障。

## 详细架构

![https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch-details.png](https://rayxyz.github.io/assets/images/ahezime/ahezime-general-arch-details.png)

平台由Nginx作为暴露在网络上的前端、平台API Gateway(API网关)、服务注册和服务发现中心（Agenble）、服务这几个大块组成。

### Nginx
Nginx负责静态内容资源文件服务和整合HTTPS保护内容网络传输安全。

### 服务注册和服务发现中心Agenble
Agenble是自主开发的微服务注册和服务发现中心中间件。当服务进程启动后会主动注册到Agenble，Agenble在服务注册完成后会发送TCP连接请求来做服务健康检查（Health Check），当检测到服务不在线时，主动移除注册的服务，保证服务的可用性。已注册服务也会主动向Agenble发送心跳，当服务被Agenble移除后，Agenble会根据服务的心跳来确定是否注册服务。这样，Agenble和服务间的双向互动在最大程度上保证服务的高可用性。

### 平台API网关
负责反向代理（Reverse Proxy）、负载均衡（LB: Load Balancing）、Rate Limiting、权限验证和API统计分析等。

API网关提供反向代理和负载均衡的服务——API网关定时请求Agenble服务注册中心，拉取已注册服务的实时信息，当得知注册中心的服务发生变更时，及时更新本地缓存的服务信息，并根据服务发送给注册中的心跳信息，找到最优的服务为下次客户端请求服务，高效地响应客户端的请求。