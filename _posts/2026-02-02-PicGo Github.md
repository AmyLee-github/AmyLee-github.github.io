---
layout:     post
title:      "PicGo上传图片到Github仓库上传失败"
date:       2026-02-02 19:38:00
author:     "JoyLee"
header-img: "img/PicGo.png"
catalog: true
tags:
    - PicGo
    - Image Bed
    - Github
---

# 背景
在日常用MarkDown写笔记和博客时，对于图片的插入，我是用PicGo将图片上传至Github仓库中，然后生成图片的链接地址的，这样可以图片就可以不受限制的在任何地方都可以同步访问。

# 问题原因
但是我最近在写笔记和博客的时候发现使用PicGo上传图片到Github仓库中总是失败，如下图所示，上传进度条总是变红。
![PicGo上传图片失败](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/PicGo%E4%B8%8A%E4%BC%A0Github%E4%BB%93%E5%BA%93%E5%A4%B1%E8%B4%A5.png)
由于我是设置的Github仓库作为图床的，因此我在`图床设置-Github`中查看我的配置。
![Github图床配置](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202195230069.png)
经过一番分析后发现，原因是我在上面配置的`Token`失效了，如下图所示，在我的Github中，我之前配置的`Token`在2025年11月28日失效了，因此我的PicGo没有权限访问我的Github仓库了，故上传图片失败。
![Github仓库Token失效](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202195658506.png)
# 解决方法
找到问题原因后，解决方法就很简单明了了，在Github中重新生成新的`Token`然后更新到PicGo的Github仓库配置中就可以了。

在Github中重新生成新的`Token`具体操作方式可以参考下面这篇知乎文章：https://zhuanlan.zhihu.com/p/1889726474673690179
中**二、手把手搭建教程（Win/Mac通用）**——**第一步：创建Github图片仓库**的操作。

将新生成的`Token`更新到PicGo的Github设置中之后，上传图片就可以成功啦！🎉

> 如果生成的不是有永久有效的Token的话，过一段时间会再次失效，可以设置一个日历提醒自己Token失效的日期，到时候记得再次重新生成。