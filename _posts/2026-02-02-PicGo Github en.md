---
layout:     post
title:      "PicGo Failed to Upload Images to Github Repository"
date:       2026-02-02 19:38:00
author:     "JoyLee"
header-img: "img/PicGo.png"
catalog: true
tags:
    - PicGo
    - Image Bed
    - Github
---

# Background
When writing notes and blogs with Markdown, I use PicGo to upload images to a Github repository and generate image links. This allows images to be accessed and synced anywhere without restrictions.

# Problem Analysis
Recently, I discovered that uploading images to Github via PicGo consistently fails, as shown in the image below. The upload progress bar turns red.
![PicGo Upload Failed](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/PicGo%E4%B8%8A%E4%BC%A0Github%E4%BB%93%E5%BA%93%E5%A4%B1%E8%B4%A5.png)
I checked my Github image bed settings and discovered the issue: my configured `Token` had expired on November 28, 2025, as shown below. Without a valid token, PicGo lacks permission to access my Github repository.
![Github Image Bed Configuration](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202195230069.png)
![Github Token Expired](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202195658506.png)

# Solution
The solution is straightforward: generate a new `Token` in Github and update it in PicGo's Github settings.

For detailed steps on generating a new `Token`, refer to this article: https://zhuanlan.zhihu.com/p/1889726474673690179

After updating the new `Token` in PicGo, image uploads work perfectly! 🎉

> Note: If the token doesn't have permanent validity, it will expire again over time. Consider setting a calendar reminder for the expiration date to regenerate a new token when needed.
