---
title: "申请了公网 IP"
date: 2023-03-15T00:27:50+08:00
tags: ["技术", "公网", "公网 IP", "OpenWrt"]
categories: ["技术"]
comments: true
---

前不久申请了公网 IP，上周末花了点时间把玩了下，分享下自己的经验。

## 如何申请公网 IP

我是联通的宽带，打 10010 转人工，直接说想申请公网 IP，对方啥也没问，就说帮我登记了，一会儿会有人联系我，过了几个小时吧，联通的另外一个工作人员来电说已经帮我在营业厅预约了，说大概需要一周的时间，实际上过了两天我查 IP 就已经是公网了，但一直没人通知，所以具体不清楚多久能好，听同事说他是电信的宽带，打完人工客服就好了。

## 怎么判断是否是公网 IP

**1. 通过访问 [ip.cn](https://ip.cn/)，可以看到对外的 IP：**

![ip.cn](https://p.madcodelife.com/blog/2024/03/b85e47d6af2a64451f191efb7aa2e935.png)

**2. 查看路由器或者软路由的 WAN IP。**

由于我家网络是由软路由分发的，所以只需要确认软路由就好了：

![软路由](https://p.madcodelife.com/blog/2024/03/41fd8a4ac8fac7e8df35029cee1b2f28.jpg)

如果你是路由器则确认路由器界面：

![路由器](https://p.madcodelife.com/blog/2024/03/ec3b531a40be379da9f4402b18705bb2.jpg)

如果两个 IP 一致，则说明所用网络是直接连接到公网的，属于公网 IP。

**这里有个坑要提醒一下**，由于家里设备默认是光猫拨号，这样家里的设备默认都是光猫的子网，那么路由器 WAN IP 一定是 `192.168.1.x`，所以需要把光猫设备从 `PPPoE` 切换成 `Bridge` 桥接模式，然后再用软路由或者路由器拨号就好了，这样就变成公网的 WAN IP 了。由于不同运营商光猫切换的方式不一样，这里就不提供教程了，网上一搜一大把。

## 怎么通过公网 IP 访问内部服务

1. **搭建服务**

   这里我用最近搭的 [Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) 打个比方，首先按照官方文档教程搭建好服务，然后你就可以通过 `http://127.0.0.1:7860/` 正常访问。

   ![intranet](https://p.madcodelife.com/blog/2024/03/ed8f924277e6b68e5d90dcbcddda5b23.jpg)

2. **开放端口**

   由于 Windows 设备防火墙的限制，你需要开放 `7860` 端口，可以参考 [Windows 防火墙允许禁止端口访问](https://docs.jdcloud.com/cn/virtual-machines/windows-firewall-allows-port-access-to-be-disabled)。

   ![firewall](https://p.madcodelife.com/blog/2024/03/afa157002d603ac8b144ec2e109285aa.jpg)

3. **转发端口**

   我是华为路由器，在路由器「更多功能」- 「安全设置」- 「NAT 服务」里面新增一条服务，指定对应端口，不同路由器用法可以自行 Google。由于我家里有软路由，所以这里需要额外在软路由再配置一次端口转发，方法也差不多。

   ![NAT](https://p.madcodelife.com/blog/2024/03/ed7951d6148ff537c5e6091a0789e14d.jpg)

👏🎉 然后就可以通过外网 IP 访问了。

![ip](https://p.madcodelife.com/blog/2024/03/d5d907f576aa46855da331cb68baa365.jpg)

## DDNS

DDNS 全称是 Dynamic Domain Name System（动态 DNS），由于外网 IP 不是固定的，每次拨号可能都会变化，并且也不方便访问，所以可以将 IP 绑定到域名上。

原理是通过轮训外网 IP，当 IP 发生变化时则动态更新域名绑定的 IP，这是绑定后的效果：

![DDNS](https://p.madcodelife.com/blog/2024/03/c0806e9b62e7c63a7881d1e269163f9e.jpg)

前提是得一个可用的域名，可以通过路由器或者软路由来绑定，这里就不赘述了。

## 总结

其实过程很简单，但其中还是有不少的坑，跑通之后可以干很多事情，这两天也试了下了 [WSL](https://learn.microsoft.com/en-us/windows/wsl/)，感觉在 Windows 设备上的开发体验也有了巨大的提升，再也不用为环境搭建发愁了，可玩性越来越高了，后续再给大家分享。

今天就这样吧，Peace out!✌️
