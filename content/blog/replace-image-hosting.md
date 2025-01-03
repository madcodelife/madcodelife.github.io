---
title: "把图床迁移到了 R2"
date: 2024-03-04T16:42:42+08:00
tags: ["技术"]
categories: ["技术"]
comments: true
---

把图床从 [Cloudinary](https://cloudinary.com/) 迁移到了 [Cloudflare R2](https://www.cloudflare.com/developer-platform/r2)，也没什么特别的原因，只是刚好看到有免费的额度，大厂嘛，感觉可能更靠谱，就迁移了，目前资源不多，很快就搞好了，这是收费标准：

|                                         | Forever Free       | Monthly Rates       |
| --------------------------------------- | ------------------ | ------------------- |
| Storage                                 | 10 GB / month      | $0.015 / GB storage |
| Class A operations: mutate state        | 1,000,000 / month  | $4.50 / million     |
| Class B operations: read existing state | 10,000,000 / month | $0.36 / million     |

装了个 [PicList](https://piclist.cn/)，用来管理图片，可以批量上传、编辑、删除，还能查看已有的图片，用起来也挺方便的，支持大部分的图床，有需要可以试试。
![上传界面](https://p.madcodelife.com/blog/2024/03/3b422b811e377080f9d271b5909b6f15.jpeg)
![内置图床](https://p.madcodelife.com/blog/2024/03/126347d21551830bb14247ced7022ea0.jpeg)
