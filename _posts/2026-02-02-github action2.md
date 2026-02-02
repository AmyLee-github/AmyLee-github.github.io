---
layout:     post
title:      "Github Pages个人博客使用Github Action部署失败2"
date:       2026-02-02 15:55:00
author:     "JoyLee"
header-img: "img/github.jpg"
catalog: true
tags:
    - github action
    - github pages
    - blog
    - jekyll
    - copilot
---

# 碎碎念（可跳过）
又想更新博客了，然后发现Github Action部署又失败了😭，开始新一轮的问题解决之路，记录一下。

# 解决过程
## 问题定位
先到Github pages的仓库中，查看历史commit，找到部署失败的commit。
![找到部署失败的commit](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164259553.png)
![找到部署失败的commit](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164553404.png)
点击❌，查看部署失败的详细信息。
![查看部署失败的详细信息](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202164826378.png)
## 分析问题（Copilot辅助+个人分析）
在报错详情页面，直接点击右上方的`Explain error`，使用Github中集成的Copilot分析报错原因。
![分析报错原因](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202165412045.png)
然后根据它给出方案去尝试解决。
![Copilot给出解决方案](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202165727896.png)
我这里，根据它给出的解释，加上我自己的理解与分析，发现是`jekyll.yml`文件中的问题，因此，我从左下方的+按钮，把我仓库中的`jekyll.yml`文件直接发送给了它，让它告诉我具体在哪一行进行修改。
<style>
    .image-row {
        display: flex;
        gap: 10px; /* 图片之间的间距 */
        justify-content: center; /* 可选：水平对齐方式 */
        align-items: flex-end;    /* 可选：垂直对齐方式 */
    }
    
    .image-row img {
        max-width: 100%;
        height: auto;
    }
</style>
<div class="image-row">
    <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202170343052.png" width="50%"/>
    <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202171155737.png" width="50%"/>
</div>

## 解决方案

**下面是Copilot给出的解决方案：**

看了您的文件，我发现您已经有安装 build-essential 的步骤（第37-38行），但可能需要添加更多依赖。

**方案一：修改第38行（推荐）**  
将第38行的命令修改为：
```yaml
run: sudo apt-get update && sudo apt-get install -y build-essential libffi-dev
```
**完整修改后的部分：**
```yaml
- name: Install build tools
run: sudo apt-get update && sudo apt-get install -y build-essential libffi-dev
```
---
**方案二：如果方案一不行，锁定 sass-embedded 版本**  
在您的 `Gemfile` 中添加或修改这一行：
```ruby
gem "sass-embedded", "~> 1.69.5"
```
这会使用一个更稳定的版本，避免 1.93.3 版本可能存在的问题。

---

**建议操作顺序：**
1. 先尝试方案一（修改第38行）
2. 如果还是失败，再使用方案二（在 Gemfile 中锁定版本）
修改后提交代码，GitHub Actions 会自动重新运行，应该就能解决构建失败的问题了。

然后我根据方案一的操作修改了`jekyll.yml`文件提交代码后就直接成功了。
![部署成功](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/20260202172745777.png)

# 反思总结

直接让Cpilot帮我进行此次的分析总结，下面是我给它的prompt：
```
我使用方案一就解决成功了，请你总结分析一下此次错误的原因
```

**Copilot的回答：**

很高兴方案一解决了问题！让我为您分析一下这次错误的根本原因：

## 错误原因分析

### 1. **直接原因：缺少 libffi-dev 依赖**
   - `sass-embedded` gem 包含 **native extensions**（本地扩展），需要编译 C/C++ 代码
   - 这个 gem 依赖于 **FFI (Foreign Function Interface)** 库来与底层的 Dart Sass 编译器通信
   - 虽然您已经安装了 `build-essential`（包含 gcc、g++、make 等基础编译工具），但缺少 **libffi-dev**（FFI 的开发头文件和库）

### 2. **为什么需要 libffi？**
   ```
   Ruby (您的 Jekyll 代码)
      ↓
   sass-embedded gem (Ruby 封装)
      ↓ 通过 FFI 调用
   Dart Sass 编译器 (本地二进制)
   ```
   - `libffi` 是一个允许不同编程语言之间互相调用的库
   - `sass-embedded` 使用 FFI 来让 Ruby 代码调用 Dart Sass 的本地编译器
   - 没有 `libffi-dev`，编译过程无法链接必要的库，导致构建失败

### 3. **日志中的关键错误信息**
   ```
   Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
   rake failed, exit code 1
   An error occurred while installing sass-embedded (1.93.3)
   ```
   这表明构建过程在尝试编译 native extension 时失败了

### 4. **为什么之前可能没有这个问题？**
   - 旧版本的 Jekyll 或 sass 处理器可能使用纯 Ruby 实现
   - `sass-embedded` 是较新的实现，依赖 Dart Sass（性能更好但需要 FFI）
   - GitHub Actions 的 runner 镜像可能更新了，移除了某些预装的开发库

## 总结

**核心问题**：构建含有 native extensions 的 Ruby gem 时，需要完整的编译工具链：
- ✅ `build-essential`：提供编译器（gcc/g++）和构建工具（make）
- ✅ `libffi-dev`：提供 FFI 库的头文件，用于跨语言调用
- ✅ 两者缺一不可

**经验教训**：在 CI/CD 环境中使用包含 native extensions 的 gem 时，要确保安装所有必要的系统级依赖，而不仅仅是基础编译工具。

# 结尾
OK，以上就是我此次解决Github Action部署失败问题的全部过程了，在这个过程中，Copilot上大分！  
感觉现在解决问题主要在途径思路，具体的解决方案，有Ai作为技术专家帮助我们，效率还是特别高的。