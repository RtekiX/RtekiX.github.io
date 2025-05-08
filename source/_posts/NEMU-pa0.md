---
title: NEMU-pa0
abstract: 重温PA实验，准备虚拟机
categories: 学习
tags:
  - ICS实验
header_image: /intro/post-bg.jpg
abbrlink: 50a57998
date: 2023-12-14 21:24:48
---

主要问题有

- 虚拟机网络问题
- github TLS问题

### 虚拟机网络

因为我主机用了代理，允许局域网连接。
想要使用主机的网络，使用的是桥接模式，复制主机的网络状态，
相当于虚拟机加入到了主机同一个局域网中，可以互相ping到。
然后系统代理选择主机的IP和相应端口，所有流量都先转发给主机做处理，
主机有点像一个默认“网关”？

------
<font color=red>2023/12/15 update

用桥接模式有一点问题，就是主机的IP是变化的，相应的代理都要手动变更。

NAT模式是虚拟了一个Vmnet交换机，交换机上连接了虚拟NAT设备、虚拟DHCP服务器和虚拟机。虚拟机通过虚拟交换机、虚拟NAT和外部主机网关连接，实现联网。另有一个虚拟网卡，实现主机和虚拟机的通信。虚拟网卡的地址是相对固定的，所以代理地址只要填虚拟网卡的地址即可。
</font>



### github TLS问题

git clone、push的时候，会出现TLS connection不安全的问题。

主要解决方法是，以自己的github账号为KEY，生成一对RSA密钥，用于SSH连接git@github.com。

本机ssh-keygen -t rsa -C "xxx"之后，将公钥添加到github账号，相当于免密登录

然后git clone和git push时要使用git@github.com/仓库地址，而不是http或https

### 顺便记录一个最简单的Makefile

hello:
	gcc hello.cpp -o hello
	
以后再学