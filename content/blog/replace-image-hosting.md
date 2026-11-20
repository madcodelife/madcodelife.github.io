---
title: "Moving My Image Hosting to R2"
date: 2024-03-04T16:42:42+08:00
tags: ["code"]
categories: ["code"]
comments: true
---

I moved my image hosting from [Cloudinary](https://cloudinary.com/) to [Cloudflare R2](https://www.cloudflare.com/developer-platform/r2). No particular reason, I just noticed the free tier and figured a big provider is probably more reliable. I don't have many assets yet, so the migration was quick. Here's the pricing:

|                                         | Forever Free       | Monthly Rates       |
| --------------------------------------- | ------------------ | ------------------- |
| Storage                                 | 10 GB / month      | $0.015 / GB storage |
| Class A operations: mutate state        | 1,000,000 / month  | $4.50 / million     |
| Class B operations: read existing state | 10,000,000 / month | $0.36 / million     |

I installed [PicList](https://piclist.cn/) to manage the images. It can batch upload, edit and delete, and browse existing images. It's handy and supports most image hosts, so give it a try if you need one.
![Upload](https://assets.madcodelife.com/blog/2024/03/3b422b811e377080f9d271b5909b6f15.jpeg)
![Built-in image hosts](https://assets.madcodelife.com/blog/2024/03/126347d21551830bb14247ced7022ea0.jpeg)
