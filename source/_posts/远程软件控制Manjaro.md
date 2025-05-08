---
title: 远程软件控制Manjaro
abstract: 用笔记本连接实验室电脑，无公网ip
categories: 学习
tags:
  - Manjaro
header_image: /intro/post-bg.jpg
abbrlink: 1075cfa9
date: 2021-11-09 16:15:34
---

### 为什么要用Manjaro
实验室电脑配置很旧，且常年不关机导致硬件老化严重，很难带动Windwos系统，于是把系统盘格式化了
受人影响想试试arch linux，又有点怕麻烦所以用了Manjaro

### 下载软件

Manjaro可以用pacma build直接构建软件

记得在teamviewer官网上把以前的电脑给删除了，不然限制用户数

anydesk也还可以用

### 解决远程软件无法显示（黑屏）

在/etc/gdm3/custom.conf，允许自动登录，填上用户名

最后一行设置`WaylandEnable=false`

### 解决无显示器导致卡顿

安装xf86，sudo pacman -S xf86-video-dummy

修改配置 sudo gedit /etc/X11/xorg.conf

抄来的配置，随便了能用就行

```
Section "Device"
    Identifier  "Configured Video Device"
    Driver      "dummy"
EndSection

Section "Monitor"
    Identifier  "Configured Monitor"
    HorizSync 31.5-48.5
    VertRefresh 50-70
EndSection

Section "Screen"
    Identifier  "Default Screen"
    Monitor     "Configured Monitor"
    Device      "Configured Video Device"
    DefaultDepth 24
    SubSection "Display"
        Depth 24
        Modes "1920x1080"
    EndSubSection
EndSection
```
