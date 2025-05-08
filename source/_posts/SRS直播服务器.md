---
title: SRS直播服务器
header_image: /intro/post-bg.jpg
abstract: 使用Simple Realtime Server搭建直播服务器
categories: 随笔
abbrlink: 1fa37b1
date: 2024-01-28 22:49:52
tags:
---
### 使用SRS-Stack构建
腾讯云轻量应用服务器提供了应用模板，在控制台重装为音视频流服务器即可快速启动SRS服务。
### 使用Docker构建
SRS文档提供了服务启动脚本，在服务器中安装好docker后运行即可。
#### 需要注意的点
1. docker hub配置镜像源，配置文件为/etc/docker/daemon.json
2. 在腾讯云控制台开放1935、8080、8000端口，用于推流和访问直播服务器管理后台
3. docker默认的推流地址是rmtp://host/live，推流码是livestream

### 遇到的问题：
- 默认推流方式需要观看者安装VLC等能接收rmtp流的播放器
- SRS提供的网页端流播放器在url中明文携带参数内容，url很长，且容易暴露信息
- 域名备案很麻烦，暂时搁置。备案不了域名就无法使用WebRTC-HTTPS。不管是访问后台还是观众观看都是不安全的IP地址访问
- 服务器带宽限制，直播很卡，只能用OKANE解决的问题