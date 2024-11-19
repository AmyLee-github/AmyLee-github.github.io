---
layout:     post
title:      "使用grad_cam对自己的模型进行可视化的Bug合集【更新ing..】"
date:       2024-11-19 17:30:00
author:     "Joy Lee"
header-img: "img/in-post/bug/dog cat.jpg"
catalog: true
tags:
    - bug
    - grad_cam
    - 热力图
    - 可视化
---
最近，因为自己的模型用到了交叉注意力机制，想对注意力的部分做关注区域的可视化工作，如下图所示。在网上搜索了解到`grad_cam`可以做热力图可视化，所以把`grad_cam`引入到自己的模型中，对交叉注意力的部分做热力图的可视化，在用的过程中遇到了各种各样的bug，在这里总结记录下来，供自己以后回顾，也供大家参考借鉴。
![热力图可视化](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/dog%20cat.jpg)
> 关于grad_cam的使用介绍，可以看B站UP主[@Larry同学](https://space.bilibili.com/326454027)的视频[【深度学习中，模型可视化，特征图的可视化，CAM热力图可视化】](https://www.bilibili.com/video/BV17i421U73P/?share_source=copy_web&vd_source=9d8fb015c2e45564f35482aa604128e4&t=905)了解基本的使用方法，视频中的代码在原视频下方评论区可以找到百度网盘链接，本文是参照的该代码在自己的模型上进行迁移使用。

# 问题一：grad_cam的input tensor输入多个的问题
在迁移到我自己的模型的时，遇到的第一个问题，就是如何输入多个input tensor的问题。
网上的使用示例方法，在用`pytorch_grad_cam.GradCAMPlusPlus`实例化之后使用的时候，输入的是一张图片的信息（一般是RGB），也就是一个tensor，但我自己的模型是需要输入多张图片（RGB，频域，区域纹理）的信息，也就是多个tensor的。1-3 是我的解决该问题的思路分析过程，只想看最终解决方法的可以跳过 1 和 2，直接看 3 。
## 1. 把多个tensor直接传入进去
如果把3个tensor直接传入进去，像下面这样：
```python
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
# images_b是区域纹理图，images_f是频域图，images是RGB图
grayscale_cam = cam(images_b, images_f, images)
```
运行的时候会报错：
`TypeError: forward() missing 2 required positional arguments: 'x_f' and 'x_p'`
就是只有第一个tensor传过去了，但是后面两个tensor没有传过去，查看GradCAMPlusPlus继承的BaseCAM源码，其中的forward函数如下：
```python
def forward(
        self, input_tensor: torch.Tensor, targets: List[torch.nn.Module], eigen_smooth: bool = False
    ) -> np.ndarray:
        input_tensor = input_tensor.to(self.device)
```
说明其中的input_tensor只接收了第1个images变量，后面2个变量没有接收，而我自己的模型的forward部分是需要3个tensor输入的：
```python
def forward(self, x_b, x_f, x_p): # x_b为区域纹理图，x_f为频域图，x_p为RGB图
```
因此会报上面的错。
## 2. 使用tuple将多个tensor打包传入
于是，我考虑是否可以将3个tensor组成一个tuple传入进去（因为之前用tensorboard可视化网络结构传参的时候可以采用这种方式），让input_tensor可以接收到tuple，从而可以得到3个tensor，于是修改代码如下：
```python
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
# images_b是区域纹理图，images_f是频域图，images是RGB图
grayscale_cam = cam((images_b, images_f, images)) # 把3个tensor组成tuple传入
```
然后还是报错：
`AttributeError: 'tuple' object has no attribute 'to'`
意思是tuple没有to的属性，这个to属性是哪里来的呢？还是看上面提到的GradCAMPlusPlus继承的BaseCAM源码，其中的forward函数：
```python
def forward(
        self, input_tensor: torch.Tensor, targets: List[torch.nn.Module], eigen_smooth: bool = False
    ) -> np.ndarray:
        input_tensor = input_tensor.to(self.device)
```
会发现里面有一个`input_tensor.to(self.device)`的操作，就是它需要把input_tensor传到当前所在的设备上（CPU或者GPU），而这个操作是tensor才有的，它接受到是tuple，所以就报这个错了。而且，再仔细看上面的参数说明的话，`input_tensor: torch.Tensor`，是**需要传入的参数是tensor类型（并且只能是一个tensor，不能接收多个）**，基于此，我想到了最后的解决办法。
## 3. ⭐多个tensor使用concat组成一个tensor
如标题所示，我想到的最后的解决办法，就是把多个tensor使用concat组成一个tensor，这样子就能满足grad_cam的使用要求了（类型为tensor，且只有一个），具体需要修改代码的地方是两个：一个是grad_cam传入的地方，还有一个就是我自己模型的forward函数。
### (1) gard_cam：
修改的代码如下：
```python
# dim=1是在通道维度进行concat，.cuda()是把concat后的tensor放到GPU上
images_all = torch.cat((images_b, images_f, images), dim=1).cuda()
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
grayscale_cam = cam(images_all) 
```
### (2) 自己模型forward函数：
```python
def forward(self, x): # 接收的参数变成1个
        x_b = x[:, :3, :, :] # 取x的第1-3个通道
        x_f = x[:, 3:6, :, :] # 取x的第4-6个通道
        x_p = x[:, -3:, :, :] # 取x的最后3个通道
```
可能重新取concat前的tensor时会有通道数不都是3的情况，可以根据自己的情况进行相应的修改，不过思路都是一样的，就是**把多个tensor使用concat组合到一起，然后在模型里面的时候再还原成原来的多个tensor**，这样子就解决了**grad_cam需要传入的是tensor类型且数量只能为1个**的问题。

至此，我遇到的第一个问题终于解决了！😭

> 因为这种方法需要改动自己原来的模型代码，做完热力图的可视化之后需要对自己原理的模型代码还原，不过这个方法的思路比较简单粗暴好理解，让我没有继续被这个Bug卡住了。。。
> 想用其他解决方案的小伙伴可以看一下下面的可能的其他解决思路链接

## P.S：可能的其他解决思路

**1.** 我在找解决方法的时候在[gard_cam源码Github仓库](https://github.com/jacobgil/pytorch-grad-cam)的[issue部分](https://github.com/jacobgil/pytorch-grad-cam/issues)，有看到一个老哥提供的解决方案，也是我的解决方案的灵感启发，不过我没有细看他的解决方法，直接用自己的思路解决了，有感兴趣的小伙伴也可以看一下这个链接：[https://github.com/jacobgil/pytorch-grad-cam/issues/279#issuecomment-1221199929](https://github.com/jacobgil/pytorch-grad-cam/issues/279#issuecomment-1221199929)

**2.** 还有一个grad_cam代码作者自己在issue中给出的一个解决方法，我尝试的时候没有成功，也可能是我没有看懂，感兴趣的小伙伴也可以看一下这个链接：[https://github.com/jacobgil/pytorch-grad-cam/issues/406#issuecomment-1500393283](https://github.com/jacobgil/pytorch-grad-cam/issues/406#issuecomment-1500393283)

> **🌟一些碎碎念：**  
> 在我找关于grad_cam可视化的bug的过程中，发现搜索不到太多中文的回答（搜到的很多都是关于grad_cam的论文解读，不是使用时的bug，也可能是我搜索的方式不太对。。。）后面才想到去[grad_cam源码仓库](https://github.com/jacobgil/pytorch-grad-cam)的issue部分对报错和问题进行搜索的，所以大家在使用grad_cam还有遇到别的bug的话，可以去[仓库的issue部分](https://github.com/jacobgil/pytorch-grad-cam/issues)搜一下，我写这篇博客也是想填补一下这方面中文回答的一点空白，或者说是提供一个这样的引子吧，希望能对大家有所帮助，有写的不对的地方也欢迎批评指正~🤗

# 问题二：还在调自己的grad_cam代码，待更新...
