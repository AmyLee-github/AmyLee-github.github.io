# 碎碎念（可跳过）

好久没有写博客了，昨天遇到一个git的问题，解决了，想把它记录下来，本来想记到本地的Markdown文件的，但后来想想还是记到自己的博客中效益更大一点，于是在我的个人博客项目文件夹下，新建了一个Markdown文件，内容写完，git commit提交然后push到Github远程之后，发现个人博客网页并没有变化（好吧，其实不是昨天才发现，去年就已经发现个人博客网页没有变化的问题，只是我当时懒，没有想着去解决😵‍💫）。

# 前置信息

我的个人博客是根据这个[B站视频](https://www.bilibili.com/video/BV12H4y1N7Q4/?share_source=copy_web&vd_source=9d8fb015c2e45564f35482aa604128e4)学习搭建的，使用的是Github Pages，然后部署也是用的Github Action自动部署。

# 问题定位

通过打开我的个人博客Github仓库，发现Github Action部署失败了：

![image-20250523160617186](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523160617186.png)

点进去发现，有报错显示`The current runner (ubuntu-24.04-x64) was detected as self-hosted because the platform does not match a GitHub-hosted runner image (or that image is deprecated and no longer supported).`当前运行的机器（`ubuntu-24.04-x64`）被识别为自托管运行器，因为它的平台与GitHub托管的运行器镜像不匹配（或者该镜像已过时且不再支持），感觉可能跟Github代码托管平台本身系统变化有关。

![image-20250523163353108](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523163353108.png)

然后我回到最开始我学习的`用Github Pages搭建个人博客`的[B站视频](https://www.bilibili.com/video/BV12H4y1N7Q4/?share_source=copy_web&vd_source=9d8fb015c2e45564f35482aa604128e4&t=114)，发现当时用的自动生成的`jekyll.yml`文件来进行Github Action的操作。

![image-20250523163504835](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523163504835.png)

![image-20250523163600585](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523163600585.png)

然后，我回到之前成功的部署的Github Action下，找到Github Action部署失败的前一次Github Action部署：

![image-20250523163754525](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523163754525.png)

点进去之后发现了很重要的Warnings：`ubuntu-latest pipelines will use ubuntu-24.04 soon.`Ubuntu的最新版流水线将很快使用Ubuntu 24.04，并给了一个链接地址[https://github.com/actions/runner-images/issues/10636](https://github.com/actions/runner-images/issues/10636)

![image-20250523163903447](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523163903447.png)

通过打开链接地址可以看到其中的公告显示：`Ubuntu 24.04 is ready to be the default version for the "ubuntu-latest" label in GitHub Actions and Azure DevOps.`Ubuntu 24.04已准备好成为GitHub Actions和Azure DevOps中“ubuntu-latest”标签的默认版本，而且是从2024年12月5日开始，到2025年1月17日完成。

![image-20250523164431332](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523164431332.png)

查看我的Github Action记录，可以看到，我最后一次成功部署的时间刚好是2024年12月4日，然后第一次部署失败的时间刚好是2025年1月18日，因此，可以判断我的部署失败确实是跟这次Github Action的系统调整有关。

![image-20250523164751730](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523164751730.png)

![image-20250523164827844](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523164827844.png)

# 问题解决

继续看前面Github Action发布的公告，发现里面有写：Switch back to Ubuntu 22 by changing workflow YAML to use `runs-on: ubuntu-22.04` We support two latest LTS Ubuntu versions, so Ubuntu 22 will still be maintained for the next 2 years.通过将工作流 YAML 文件中的 `runs-on` 改为 `ubuntu-22.04`，可以切换回 Ubuntu 22。我们支持两个最新的长期支持（LTS）版本的 Ubuntu，因此 Ubuntu 22 将在未来两年内继续得到维护。也就是说，我只要把yml文件里面修改成`runs-on: ubuntu-22.04`，就还可以继续苟两年🤭（两年之后的事，到时候再说吧😅）

![image-20250523165229157](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523165229157.png)

于是，解决思路就比较清晰了，打开之前`.github\workflows`路径下的`jekyll.yml`文件，把之前里面的`runs-on: ubuntu-latest`修改成`runs-on: ubuntu-22.04`（并在后面注释一下之前的官方公告的链接，方便之后溯源），然后把修改使用git提交，并push到Github远程仓库，然后就能够再次部署成功啦！🎉

![image-20250523165850696](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523165850696.png)

![image-20250523170336291](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20250523170336291.png)

# 总结

## 部署失败原因：

Github Action官方从2024年12月5日开始，到2025年1月17日对`ubuntu-latest`默认系统做了调整，之前是`ubuntu-22.04`，现在改成了`ubuntu-24.04`，因此之前的yml文件里面还是写的`runs-on: ubuntu-latest`会出现系统性的报错。

## 解决方法：

解决方法很简单，就是把`.github\workflows`路径下的`jekyll.yml`文件里面的`runs-on: ubuntu-latest`修改成`runs-on: ubuntu-22.04`，不过需要注意的是，这个方法算是临时方法，只能管两年。