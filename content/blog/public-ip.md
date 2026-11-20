---
title: "Getting a Public IP"
date: 2023-03-15T00:27:50+08:00
tags: ["code"]
categories: ["code"]
comments: true
---

I recently got a public IP for my home network and spent some time playing with it last weekend. Here's what I learned.

## How to get a public IP

My broadband is from China Unicom. I called 10010, asked for a human agent and said I wanted a public IP. They didn't ask anything, just said they'd registered my request and someone would contact me. A few hours later another Unicom staff member called to say they had booked it at the service center and it would take about a week. In practice, two days later my IP was already public, but nobody ever told me, so I'm not sure exactly how long it takes. A colleague with China Telecom broadband said his was done right after the call.

## How to tell if you have a public IP

**1. Visit [ip.cn](https://ip.cn/) to see your external IP:**

![ip.cn](https://assets.madcodelife.com/blog/2024/03/b85e47d6af2a64451f191efb7aa2e935.png)

**2. Check the WAN IP on your router or soft router.**

My home network is served by a soft router, so I only need to check that:

![Soft router](https://assets.madcodelife.com/blog/2024/03/41fd8a4ac8fac7e8df35029cee1b2f28.jpg)

If you use a regular router, check the router's admin page:

![Router](https://assets.madcodelife.com/blog/2024/03/ec3b531a40be379da9f4402b18705bb2.jpg)

If the two IPs match, your network is directly connected to the internet, i.e. you have a public IP.

**One gotcha worth mentioning:** by default the optical modem (ONT) does the dial-up, so every device at home sits on the modem's subnet, and the router's WAN IP will always be `192.168.1.x`. You need to switch the modem from `PPPoE` to `Bridge` mode and let your soft router or router do the dial-up instead; then the WAN IP becomes public. The steps differ between ISPs, so I won't cover them here; there are plenty of guides online.

## How to reach home services through the public IP

1. **Set up a service**

   I'll use the [Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) I set up recently as an example. Follow the official docs to get it running, and you can access it at `http://127.0.0.1:7860/`.

   ![intranet](https://assets.madcodelife.com/blog/2024/03/ed8f924277e6b68e5d90dcbcddda5b23.jpg)

2. **Open the port**

   Because of the Windows firewall, you need to open port `7860`. See [Windows Firewall: allowing or blocking port access](https://docs.jdcloud.com/cn/virtual-machines/windows-firewall-allows-port-access-to-be-disabled) (in Chinese).

   ![firewall](https://assets.madcodelife.com/blog/2024/03/afa157002d603ac8b144ec2e109285aa.jpg)

3. **Forward the port**

   I have a Huawei router: under "More Features" - "Security" - "NAT Service", add a rule for the port. Other routers differ, so Google is your friend. Since I also have a soft router, I had to set up port forwarding there as well, which works much the same way.

   ![NAT](https://assets.madcodelife.com/blog/2024/03/ed7951d6148ff537c5e6091a0789e14d.jpg)

👏🎉 Now you can access it via your external IP.

![ip](https://assets.madcodelife.com/blog/2024/03/d5d907f576aa46855da331cb68baa365.jpg)

## DDNS

DDNS stands for Dynamic Domain Name System. Your external IP isn't fixed and may change every time you dial up, and IPs aren't easy to remember anyway, so you can bind the IP to a domain name.

It works by polling your external IP and updating the domain's DNS record whenever the IP changes. Here's what it looks like once bound:

![DDNS](https://assets.madcodelife.com/blog/2024/03/c0806e9b62e7c63a7881d1e269163f9e.jpg)

You'll need a domain name first. The binding can be done on your router or soft router; I won't go into detail here.

## Wrapping up

The process is simple, but there are quite a few pitfalls along the way. Once it works you can do a lot with it. I also tried [WSL](https://learn.microsoft.com/en-us/windows/wsl/) these past few days, and the development experience on Windows has improved hugely. No more headaches setting up environments, and there's more and more to play with. I'll share more later.

That's all for today. Peace out! ✌️
