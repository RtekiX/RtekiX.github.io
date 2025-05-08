---
title: 关于FCM服务的一些问题
header_image: /intro/post-bg.jpg
abstract: 为了让手机能够及时收到邮件推送
categories: 随笔
abbrlink: 24b990d7
date: 2024-02-06 22:17:13
tags:
---
## 起因
昨天晚上，突然发现QQ邮箱app即使允许电池无限制使用，收到邮件的时候也不会推送通知，查了一下是因为三星没有像mipush一样的推送服务，QQ邮箱只有常驻后台运行才能通知。

于是为了能及时收到信息，我下载了支持fcm的gmail和outlook，测试能不能利用fcm推送

## 问题排查
1. **电池优化问题**
   - 国行安卓手机没有统一的推送服务，大多只能靠应用后台保活来实现实时通知，比如QQ和微信就是默认无限制运行，允许任意自启动和重启，所以才能随时收到消息。
   - 经过排查，所有支持fcm的应用都设置了无限制运行，允许常驻，仍然无法收到通知
2. **FCM问题**
   - 允许应用自启动，但是没有收到消息，说明outlook和gmail并没有收到来自FCM的通知，遂检查FCM连接状况。
   - 三星国行禁用了<font color="red">\*#\*#426#\*#\*</font>查看fcm连接状况，只能通过第三方app进入诊断页面。我在github找到了一个FCM推送查看器。在log里可以看到，FCM服务是断开的，每隔十几分钟会尝试重连，然后连接失败，log为`Connecting using McsConnection{NetworkWrapper{244},type=0,isVpnConnected=true}`
   - 看到这个log，我以为是clash的问题，于是关闭代理再次连接，成功连接上。所以这里我以为是fcm服务，也就是mtalk.google会检测代理连接，必须直连，所以花了很长一段时间想在配置文件里加上混合配置或者parser。但问题在于，我另一个手机使用相同的配置，能够长期稳定连接到fcm服务不断开，所以问题根源不在这里。
3. **DNS问题**
   - 绕了很长一段远路，最后想起来clash有日志捕捉功能，遂开启。很快找到原因，`error: all DNS requests failed, first error: use default dns resolve failed: all DNS requests failed, first error`。所以是DNS问题，尝试了三个解决手段：
     - 关闭clash中的DNS劫持：无效
     - WIFI设置手动DNS：无效
     - clash中设置强制启用系统hosts和DNS：有效

## 结果
所以根本上是clash配置文件中的DNS server有一些问题。但还有一点不明白的地方：在测试连接的时候，我发现只有 <font color="red">5G网络+开启代理</font> 两者组合会导致DNS出现问题。使用2.4GHZ的WIFI或者4G移动数据+代理，可以连接。使用5GHZ的WIFI或者5G移动数据，可以连接。但5G+代理，不修改DNS设置就会出现问题，这一点仍然摸不着头脑。