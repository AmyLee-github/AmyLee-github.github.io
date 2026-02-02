---
layout:     post
title:      "VSCode中Project Manager插件卸载后项目颜色不恢复"
date:       2025-11-06 22:32:00
author:     "JoyLee"
header-img: "img/vscode-project-manager.png"
catalog: true
tags:
    - VSCode
    - Extensions
---

VSCode中的Project Manager插件是一个可以在打开不同的项目的时候，给VSCode界面着不同的颜色的一个插件，这样可以方便快速分辨不同的项目，在同时打开多项目进行代码编辑的时候提高效率。

但是感觉有点麻烦，提高的效率并不多，因此没有再使用该插件了，但是在卸载Project Manager插件之后，发现原来设置了不同颜色的项目，仍然会显示原来的颜色，感觉有点没有处理干净，这里给出解决这个问题的方法。

点击VSCode顶端的搜索框，输入">"搜索“workspace settings”，选择“Preferences: Open Workspace Settings (JSON)"，然后将打开的json文件里面的内容删除干净保存之后，项目的VSCode界面就恢复正常了。

![image-20251107095418405](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20251107095418405.png)

