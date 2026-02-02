---
layout:     post
title:      "Project Color Not Reset After Uninstalling VSCode Project Manager Plugin"
date:       2025-11-06 22:32:00
author:     "JoyLee"
header-img: "img/vscode-project-manager.png"
catalog: true
tags:
    - VSCode
    - Extensions
---

The Project Manager extension in VSCode is a plugin that allows you to assign different colors to the VSCode interface for different projects. This makes it easier to quickly distinguish between projects, improving efficiency when working on multiple projects simultaneously.

However, I found it a bit cumbersome and not significantly efficient, so I decided to stop using the plugin. After uninstalling the Project Manager extension, I noticed that the projects previously assigned different colors still retained their original colors. It seemed like the settings were not completely cleared. Here's how to resolve this issue:

Click on the search bar at the top of VSCode, type ">" to search for "workspace settings" and select "Preferences: Open Workspace Settings (JSON)." Then, delete all the content in the opened JSON file and save it. After this, the VSCode interface for the projects will return to normal.

![image-20251107095418405](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20251107095418405.png)

