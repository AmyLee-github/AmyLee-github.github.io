---
layout:     post
title:      "Using Wandb: Terminal Shows Successful Run, But Why Can't I See Updates on the W&B Webpage After Refreshing?"
date:       2024-11-22 16:00:00
author:     "Joy Lee"
header-img: "img/in-post/bug/wandb.png"
catalog: true
tags:
    - bug
    - wandb
    - account
---

# Solution at a Glance:

> **Check if you are logged into the correct account!!!  
> Check if you are logged into the correct account!!!  
> Check if you are logged into the correct account!!!**

# Detailed Notes:
Recently, while using Wandb's Table feature to save some data for analysis, my code ran successfully, and the terminal output looked fine, as shown below:  
![Wandb Run Output](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/101d1a189cfa4b6eafbe7c0b4a68ead0.png)  
However, when I clicked the link in the terminal, it returned a 404 error. Even after refreshing my W&B dashboard, I couldn't see any updates to the Table I had just saved.  
![Webpage Error](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/183ca63202904b39b2dd870e393dda73.png)

I started troubleshooting my code, wondering if the issue was caused by reusing the same Table name or project name. I tried changing them multiple times, but nothing worked. Since there were no errors, I had no clue what was wrong. 😭 Then, during one of the runs, I happened to glance at the initial Wandb output and noticed: `Currently logged in as: XXX`  
![Incorrect Account Name](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/a6826fdeaecd4a0e94975de73c9a8cb3.png)

> "Huh? 🤔 That account name isn't mine! Then I remembered that I had shared a Wandb Table link with my labmate earlier. Could it be her account?!"

I immediately asked my labmate to check her W&B dashboard, and sure enough, all 60+ runs I had just submitted were in her account. 😂

Once I identified the issue, the solution was simple: use `wandb login --relogin` in the terminal to log back into my own account.

As for why my account had switched to my labmate's Wandb account, the reason was straightforward: **we were both running experiments on the same server!** She must have logged into her account using `wandb login --relogin`, and I didn't notice. I assumed I was still logged into my account and started submitting runs, only to find no updates on my W&B dashboard. 😂

So, **if you're sharing a server with your labmates and using Wandb, always double-check that you're logged into your own account before running experiments.** Otherwise, your experiment logs might end up in someone else's account, leaving you puzzled for hours. 😭

To check the currently logged-in Wandb account, use:
```bash
wandb login
```
The output will look like this:

![Check Wandb Account](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241204145037169.png)

If the account name is yours, you can proceed. If not, log in again using:
```bash
wandb login --relogin
```

This is a funny yet frustrating bug that I wanted to document for anyone who might encounter the same issue. Hopefully, this helps!
