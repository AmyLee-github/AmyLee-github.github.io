---
layout:     post
title:      "Deepfake detection using deep learning methods: A systematic and comprehensive review"
date:       2024-11-27 23:00:00
author:     "Joy Lee"
header-img: "img/in-post/paper/deepfake.png"
catalog: true
tags:
    - 论文笔记
    - Deepfake Detection
    - Review
---
## Abstract

根据其应用对深度伪造检测方法进行**分类**：
视频检测、图像检测、音频检测、混合多媒体检测

本文的**目的**是让读者更好地了解：
(1)如何产生和识别深度伪造
(2)该领域的最新发展和突破
(3)现有安全方法的弱点
(4)需要更多调查和考虑的领域

**结果**表明：
传统神经网络（**CNN**）方法是出版物中最常用的深度学习方法。
大多数文章都是关于**视频**深度假检测的主题。
大多数文章只关注一个参数的增强，其中**精度**参数受到的关注最多。

## Contents

<div style="text-align: left;">
	<img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127163442667.png" alt="image-20241127163442667" style="zoom:67%;" />
</div>

## 1 Introduction

本研究采用**系统文献回顾法**（Systematic Literature Review, SLR）查找、分析和结合相关研究的结果。
深度伪造检测中使用的**四种主要深度伪造策略**：图像深度伪造检测、视频深度伪造检测、声音深度伪造检测、混合多媒体深度伪造检测。
我们评估了在该领域应用深度学习方法的每个类别和方法的许多特征，如**优势、障碍、数据集、用法、模拟环境、安全性、迁移学习**。

本文讨论了深度学习方法在深度伪造检测中的使用，并解决了各种问题。我们还对今后的工作作了详细的研究，突出了需要纠正的问题。
简而言之，本文的贡献如下：
• 提供深度伪造检测领域**深度学习主题**的广泛概述；
• 提供DL-deepfake检测**现有方法**的全面概述；
• 概述了深度学习中包含深度伪造检测的**关键方法**；
• 研究具有关键特征的DL-deepfake检测的**每种策略**；
• 强调上述技术可以改进的**关键领域**。

本文的结构：
第2节——讨论深度伪造检测中深度学习的基本概念和相关术语
第3节——相关研究
第4节——研究方法和技术的文章选择
第5节——所选文章的类别
第6节——结果和比较
第7节——未解决的问题
第8节——结论

表1：使用的缩略语
![image-20241127171323695](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127171323695.png)

## 2 Basic Concepts and Corresponding Terminologies
<div style="text-align: left;">
    <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127171626539.png" alt="image-20241127171626539" style="zoom:75%;" />
</div>

### 2.2 Deepfake detection, fake image detection, and fake video detection

关于深度伪造图像或视频检测可分为两种检测方法：基于**常规神经网络（CNN）**的方法和基于**区域常规神经网络（RCNN）**的方法(X. Zhou et al, 2020)。

- 基于**CNN**的技术从视频帧中获取面部图像，并将其输入CNN进行训练和预测，以获得图像级的结果。因此，在深度伪造视频中，这样的算法只使用单帧的空间信息。
- 基于**RCNN**的技术需要一系列视频帧进行训练，以产生视频级别的结果。这种方法被称为RCNN，它结合了CNN和递归神经网络（RNN） （Tariq et al, 2021）。因此，基于RCNN的技术可以在深度伪造电影中充分利用时空信息（Chung et al, 2015）。

视频具有各种不同帧之间的时间顺序属性，这使得检测单个欺诈性图像的技术具有挑战性。该子类别侧重于深度伪造视频识别方法，并将其分为两组：**使用时间特征的组**和**研究帧内视觉伪影的组**（Mehta et al, 2021）。

同时，某些深度伪造视频生成模型无法合成人脸的所有纹理，从而产生一些粗糙的假人脸。例如，脸上的小皱纹不能准确地产生。与此同时，在创建虚假帧的最后阶段，创建的人脸被融合到背景中。平滑技术经常被用来缓解这个过程中产生的边界不一致，这导致了面部纹理特征的丢失。图1a显示了真实视频VidTIMIT数据集的一帧，而图1b显示了人脸处理视频DeepFake-TIMIT数据集的一帧。人的眼睛，随着图形，很难区分哪一帧是真实的。不过，真实帧的纹理更逼真，比如双眼皮、眼周皱纹等特征，而假帧缺乏纹理细节。

![image-20241127210159032](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127210159032.png)

### 2.5 Domain adaptation and TL

一般理解TL及其在深度伪造检测中的应用是该领域的一个重要组成部分。然而，TL是DL的一个子领域，它利用来自源领域的信息来帮助方法更快、更有效地从目标领域获取知识。TL在伪造领域引起了很大的兴趣。在训练之前将ImageNet的预训练权值加载到模型中是一个简单的TL （Marei et al, 2021）。大多数使用深度域自适应的工作依赖于差异测量。此外，还有一些基于领域对抗学习和差异评估的研究（I. Ahmed et al, 2021）。因此，TL是一种使用先前学习的模型信息解决新任务（明显相关或不相关）的方法，只需很少的再训练或微调。与传统的机器学习方法相比，深度学习需要大量的训练数据。当使用TL时，训练特定领域问题所需的时间大大减少（Lewis et al, 2020）。使用基于语言的深度学习方法缓解数据集（Hung & Chang, 2021）。由于深度伪造在互联网上的增长是如此可怕，因此在当前情况下，检测它们已成为当务之急（Heidari等人，2022）。该领域的研究人员正在努力开发一些潜在的TL机制，以帮助减轻这种情况下的挑战。

## 3 Relevent Reviews

P. Yu等（2021）介绍了深度伪造视频制作技术，分析了可用的检测系统，并讨论了研究方向的路径。他们的论文提供了**深度伪造视频检测**的研究现状，即制作过程、各种检测算法和目前的标准。然后讨论了多种检测技术。该审查依赖于当前检测算法的新困难以及潜在的发展。他们的研究非常强调泛化和弹性。

## 4 Methodology of Research
**SLR**：对某一特定主题的所有研究的综合检查
研究问题、选择标准

<div style="text-align: left;">
    <img src="C:/Users/JoyLee/AppData/Roaming/Typora/typora-user-images/image-20241127211944761.png" alt="image-20241127211944761" style="zoom:75%;" />
</div>
### 4.1 Question formalization

该研究的主要目标是识别、区分、评估和评估深度伪造检测方法中发现的所有相关出版物。SLR可以用来探索方法的各个方面和特征，以达到前面指定的目标。SLR的另一个目标是更好地了解这个行业遇到的主要问题和问题。已定义的几个研究问题（RQs）如下：

- RQ-1. 深度学习在深度伪造检测中的应用是什么？
  第1节讨论了这个问题

- RQ-2. deepfake中的深度学习方法是什么？它们有什么用途？
  这个问题的答案在第2部分

- RQ-3. 在这个领域有没有发表过评论文章的研究？这篇文章与以往的研究有何不同？
  这个问题的答案在第3部分

- RQ-4. 这个领域的主要问题和未解决的问题是什么？
  第5部分将给出本主题的答案，而第7部分将给出尚未解决的问题

- RQ-5. 我们如何找到文章并选择深度伪造检测DL方法？
  这将在第4.2节中处理

###   4.2 The process of article selection

本研究的文章检索和选择过程分为四个部分。图2描述了这一点。表3显示了第一步中用于搜索出版物的关键字和术语。这个集合中的文章是电子数据库检索的结果。使用的一些电子数据库有谷歌Scholar、Scopus、IEEE、ACM、Springer、Elsevier、Emerald Insight、Taylor & Francis、Wiley、Peerj和MDPI。其他被发现的项目包括期刊、会议文章、书籍、章节、笔记、技术研究和特刊。第一阶段交付了651篇文章。此外，发布者的文章分布如图3所示。

![image-20241127213129906](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127213129906.png)

![image-20241127213250349](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127213250349.png)

![image-20241127213533300](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127213533300.png)

最终要研究的文章数量在第二阶段通过两个过程确定。使用图4中列出的标准对文章进行初始评估（阶段2.1）。目前还剩下254条。在图5中，展示了出版商的文章分布。2.2阶段排除综述文章；在前一阶段，剩余254篇文章中8.87%为综述性文章。IEEE出版了大部分的研究出版物（21%）。Springer和Elsevier发表了最多的综述文章（5.54%）。目前有210篇文章可用。在第三阶段，对文章的标题和摘要进行审查。文章的方法、评估、讨论和结论都经过反复检查，以确保它们与研究相关。目前，已选出88篇文章进行进一步审查。最后，34篇文章被挑选出来审查和检查其他出版物，因为它们符合严格的标准。所选文章的发布者分布如图6所示。IEEE发表了大部分入选文章。Wiley、IGI Global Publishing和Tech Science Press的排名最低。2021年和2020年发表的论文数量相等（50%，16篇）。发布出版物的期刊如图7所示。IEEE Access期刊发表的文章最多（9.4%，3篇）。表4还列出了所选物品的规格（更新日期为2021年7月19日）。此外，在接下来的部分中，对深度伪造检测机制进行了深入的探讨，并对其性质进行了讨论。

![image-20241127213752213](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127213752213.png)

![image-20241127213913406](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127213913406.png)

![image-20241127214051301](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127214051301.png)

![image-20241127214321940](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127214321940.png)

<img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/112721525306_01.png" alt="112721525306_01" style="zoom:150%;" />

## 5 Deepfake Detection Mechanisms
<div style="text-align: left;">
    <img src="https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127215701397.png" alt="image-20241127215701397" style="zoom:75%;" />
</div>

### 5.1 Fake image detection

如第2节所述，假人脸图像检测是图像伪造检测领域最困难的挑战。虚假照片可以在社交媒体平台上构建虚假身份，从而允许非法窃取个人信息。例如，假图片生成器可以用来制作内容不合适的名人照片，这可能是危险的。本节将介绍如何使用深度学习方法检测假图像。Deepfake的发展大大降低了人脸制造技术的门槛。特别是Deepfake，它使用gan将原始图像的脸替换为另一个人的脸。由于GAN模型是在100张照片中的10张照片上进行训练的，因此它们更有可能生成能够准确拼接到主图像中的逼真人脸。通过适当的后期处理，图像可以变得更加真实。此外，随着深度学习的发展，换脸创新已被应用于各种环境，包括隐私保护、多媒体合成和其他新应用(M. Zhang， Chen等，2021；张、陈等，2020)。

因此，W. Zhang， Zhao等人（2020）研究了一种全面的基于DL的伪造特征提取方法，该方法可以成功区分使用DL生成的真实和伪造图像。他们的伪造特征提取方法可以揭示基于深度学习和错误水平分析（ELA）的人脸交换图像，可以成功地区分深度学习生成的人脸图像。代替原始照片，他们利用这些ELA图像来训练CNN模型。将原始图像转换为ELA图像是提高CNN模型训练效率的一种方法。由于ELA图像的信息量比原始图像少，因此可以提高效率。ELA图像特征集中在原始图像中误差水平超过阈值的部分。此外，ELA图像中的像素通常与相邻像素明显不同，对比度非常明显；因此，ELA处理后的图像发挥了训练CNN模型的有效性。他们训练了一个CNN模型来提取ELA照片的伪造方面，然后确定输入的图像是否为假。他们检验了这种方法，并在实践中证明了它的实用性。由于当误差水平大于阈值时，前面阶段创建的ELA图像可能会强调原始图像的特征，因此只需要两个卷积层。因此，它更容易分离伪造特征和评估图像是否真实。结果表明，利用ELA分析的图像特征可以有效地识别图像的真实性。此外，他们的测试表明，ELA技术大大提高了CNN模型的训练效率。他们评估了这一策略，并在实践中证明了其有效性。研究结果表明，利用ELA处理后的图像特征可以有效地判断图像的真实性。此外，他们的研究表明，ELA方法可以显著提高CNN模型的训练效率。特别是，如果所需的浮点计算能力降低90%以上，则可以大大减少处理量，而不会损失精度。

此外，Lee等人（2021）还提出了具有有效面部操作检测过程的手工人脸操作（HFM）图像数据集和软计算神经网络模型（Shallow-FakeFaceNets）。神经网络分类器模型Shallow-FakeFaceNet （SFFN）展示了通过关注改变的面部标志来识别虚假图片的能力。检测过程仅使用红绿蓝（RGB）信息来检测欺诈性面部图像，不使用任何元数据，这些元数据很容易被更改。该技术在接收人工作特征下面积（Area Under Receiver Operating Characteristic， AUROC）方面表现较好，在识别手工制作的假人脸图像上获得了3.99%的f1得分和2.91%的AUROC，在检测gan生成的小幅假人脸图像上获得了93.99%的f1得分和10.44%的AUROC。该方法旨在开发一种自动化防御系统，利用其尖端的HFM和SFFN，打击各种在线服务和应用程序中使用的虚假图片。此外，该研究提供了各种测试结果，可用于成功推动未来应用软计算研究，以成功识别人类和gan生成的虚假人脸图像。

此外，作为预处理，Guo等人（2021）开发了一个简单但成功的自适应操作痕迹提取网络（AMTEN）组件，该组件使用卷积层作为预测器来检索照片处理痕迹。在反向传播过程中，权重是动态调整的。在连续的层中重复轨迹以优化操作轨迹。此外，他们还将AMTEN和CNN结合在一起，创造了假脸检测器AMTENnet。AMTEN的操纵痕迹通过CNN呈现，形成不同的判别特征。进行了一组测试，其中选择了许多常见的后处理过程来复制复杂情况下的实际取证。他们的研究结果表明，AMTENnet获得了更高的检测精度以及期望的泛化能力。此外，AMTENnet在混合假脸（HFF）数据集上的平均检测准确率比其他方法高出7.61%。实际上，AMTEN是这里的主要优势，因为它比Constrainedconv和Spatial Rich Model （SRM）产生更好的残差提取。确实值得注意的是，AMTEN可以作为一个简单的残差预测器用于额外的面部法医程序。

此外，Guarnera等人（2020）完成了先前关于深度假图像分析的研究。期望最大化技术用于提取卷积痕迹（CTs）：一种独特的指纹，可用于确定照片是否为深度伪造以及生成它的GAN架构。检索到的CT是一种指纹，具有较强的判别能力和抗攻击能力，不受高级图像思想的影响。研究结果还表明，一种简单且计算速度快的方法可以胜过最先进的方法。此外，CT与图像制作过程相连，通过旋转输入图像来定位最重要的方向，可以很好地实现更好的性能。CT的总体分类准确率超过98%，并对人脸图像中包含的10种不同GAN架构进行了深度造假，证明了CT是值得信赖的，独立于图像语义。最后，对FACE应用程序创建的深度伪造进行测试，在深度伪造检测任务中达到93%的准确率，证明了该方法在现实环境中的效率。

此外, I.-J. Yu等人（2020）引入了操作分类网络（MCNet），通过利用几个领域特征对联合摄影专家组（JPEG）压缩图片上使用的各种操作技术进行分类。为了优化JPEG压缩图像的操作分类效率，MCNet使用空间、频率和压缩域数据。他们创建了特定领域的预处理和网络拓扑。结果表明，MCNet优于现有的操作检测网络以及许多低级视觉网络。所建议的网络非常适合微调当前的取证任务，如深度伪造检测和JPEG图像完整性验证，因为它考虑了许多替代的更改方法。结果表明，MCNet优于现有的操作检测网络以及许多低级视觉网络。所建议的网络非常适合微调当前的取证任务，如深度伪造检测和JPEG图像完整性验证，因为它考虑了许多替代的更改方法。

J. Yang等人（2021）识别出真实和虚假面部图像之间的微小纹理差异，并使用图像显著性方法来展示它们。根据这一发现，他们使用增强的引导滤波器对所有假照片和真实照片进行图像预处理，旨在改善人脸修改图像中包含的纹理伪影，我们称之为引导特征。然而，这种扭曲无法在视觉上捕捉到；这些放大的纹理变化将使用Resnet18网络学习，该网络可以不断地下采样和重新采样，以准确识别真实和虚假的面部图像。研究表明，该方法在全图像和人脸图像训练中都具有最好的检测精度，处于当前研究的前沿。同时，由于极大扩展的纹理特征，使得网络能够快速准确地记录差异，更快地实现收敛，减少训练时间。然而，他们的方法存在一定的缺陷，这是研究真假人脸识别时经常遇到的问题。网络模型训练仍然需要大量的数据来获得合理的准确性。而不是建立一个通用的检测网络，训练一个新的认证网络仍然需要未知人脸篡改方法。

最后，Hsu等人（2020）提出了一种基于假特征网络的配对学习方法，用于识别由最先进的gan创建的假人脸和通用图片。它们可以通过结合跨层特征表示来学习中高级特征和判别错误特征。然后将简化后的DenseNet转换为可以接受配对数据作为输入的双流网络结构。然后，该网络被训练使用配对学习来区分假照片和真照片的特征。最后，在建议的常见伪特征网络中添加分类层，以确定输入图像的真假。他们的配对学习系统允许假特征学习，允许经过训练的假图像检测器识别由新的GAN创建的假图像，即使它没有包含在训练阶段。研究结果表明，他们的技术在准确率和召回率方面击败了其他最先进的方法。

表5讨论了deepfake中使用的deepfake图像应用程序及其属性。

![image-20241127221424372](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/image-20241127221424372.png)

### 5.2 Fake video detection

如第2节所述，最困难的问题之一是检测视频深度伪造。要使用deepfake创建一个非常高级的换脸电影，只需要一个GPU和大量的训练数据。几位科技爱好者在短视频平台上发布了大量的现场表演视频，这些视频用合成明星的脸代替了普通人的脸，引发了一场热议。人们对这种技术提出了担忧。在这项技术之前，人们普遍认为视频是可靠的，甚至可以在多媒体取证中用作视频证据。在数字时代，视频深度造假技术对公众信心构成了威胁。一些人甚至认为这种创新会扼杀社会发展。由于开源数据集的可用性迅速增加，gan等主题的研究取得了重大进展，以及高速计算领域的重大技术发展，就创建被操纵序列的成本而言，创建和操纵假视频已经成为一项相对简单的任务。通过将政治家的脸叠加在目标演员的脸上，深度伪造电影可能被用来诽谤和促进虚假的政治宣传。此外，它还可能被用来使主要政治机构难堪、羞辱或分裂，通过破坏世界各国人民、政府和国家之间微妙的和平平衡，危及国家和平。因此，检测此类欺诈性视频变得非常重要。越来越多的学者开始关注检测技术以及真假电影之间的区别。**传统技术**和**深度学习**方法是最常用的。此外，人脸识别中的**活体识别**和**多媒体取证**也可以提供选择。目前，人们认为深度学习可以构建逼真的假脸，检测和调查不可见的伪造痕迹，并识别伪造电影。与标准的图像取证技术不同，深度学习方法将特征提取和特征分类纳入网络结构，实现了端到端有效的自动特征学习分类方法（Zheng, Liu, Ni, et al ., 2021）。另一方面，典型的图像取证方法通常不适合视频，因为压缩会显著降低数据质量。随着深度学习在数字取证领域的进步，它颠覆了标准的图像取证方法。因此，考虑到深度伪造电影的全球影响力以及这些视频可能危害和平与安全的程度，检测深度伪造电影的自动化方法变得极其重要。

在本节中，我们将研究如何使用深度学习方法检测假视频。因此，Güera和Delp（2018）提出了一种自动检测深度假电影的时间感知方法。他们展示了一个自动检测深度伪造视频的时间感知系统。他们展示了一个强大的可训练的循环深度假视频识别系统。对于处理帧序列，建议的系统采用卷积LSTM结构。卷积LSTM有两个重要组成部分：(1)CNN用于提取帧特征。(2)利用LSTM进行时间序列分析。给定一个未知的测试序列，他们检索了CNN为每一帧生成的特征集合。然后将多个连续帧的特征串接并发送到LSTM进行分析。最终，他们计算了这个序列是深度伪造的还是未经处理的视频的几率。然后使用这些属性来训练RNN，以确定视频是否被操纵。他们用从各种视频网站上收集的大量深度伪造电影来测试他们的策略。他们演示了如何，尽管一个最小的架构，系统可以在这个任务中获得竞争性的结果。他们对大量经过数字修改的视频进行了实验，结果表明，一个简单的卷积LSTM结构确实可以有效地预测视频是否被操纵，只需2秒的视频数据。

此外，X. H. Nguyen等人（2021）提出创建三维（3D）图像以及可以从3D图像中学习空间和时间维度特征的3D CNN模型。他们使用3D卷积核创建深度3D CNN，学习短连续帧序列的时空特征，以识别深度假视频。在他们的方法中，卷积层中的特征映射与前一层的连续帧相关联。因此，它可以在一系列帧中收集人脸在空间和时间维度上的信息。研究表明，他们建议的模型在深度伪造的face取证和VidTIMIT数据集上表现非常好。由于提升的视频，建议的模型在两个数据集上都优于99%以上。

此外，Jung等人（2020）设计了一种方法，通过分析眨眼（一种自发和无意识的人类行为）的实质性变化来检测gan模型产生的深度伪造。眨眼模式因人的性别、年龄、认知活动和一天中的时间而异。因此，他们的系统利用机器学习、各种方法和一种启发式方法来验证深度伪造的完整性来检测这些变化。他们的方法是利用先前的实验结果开发的，不断显示出验证深度伪造和常规视频完整性的巨大可能性，正确识别了8个视频中的7个（87.5%）深度伪造。然而，这项研究的一个缺点是，眨眼也与精神疾病和多巴胺活动有关。完整性验证可能与患有精神障碍或神经传导通路有问题的人无关。相反，由于网络安全攻击和防御总是在不断发展，这可能会通过各种技术得到增强。该技术为克服完整性验证算法的局限性提供了一条新的途径。

然而，Karandikar等人（2020）建议在CNN模型上使用TL来训练数据集，并专注于人脸修改以识别假货。他们的分类器建立在VGG-16模型之上，然后辅以批处理归一化、dropout和专有的双节点密集层。建议架构的最后一个密集层中的两个节点用于两个最终类（真实类和虚假类）。批规范化层用于对前一层的输入进行规范化和缩放。还包括Dropout，以防止过拟合和改善权重优化。在每个epoch， dropout层将任意传输一些节点，使其脱离前一层。这将导致更好的训练，因为这一层在权重更新过程中引入了一些不可预测性。他们的方法表现良好，可以有效地收集额外处理测试深度伪造所需的特征。对于低质量的图像，该模型的准确率会下降，对于中等质量的视频，必须通过使用混合模型对时间参数进行训练来进一步提高准确率。因此，更好的训练将来自更高质量的更好的数据集。

此外，Z. Zhao， Zhou等人（2021）提出了一种更准确的深度假视频人脸欺诈检测方法。这提供了一个多层融合神经网络（MFNN），可以在三个不同的层中捕获由深度伪造引起的各种伪物。该技术通过结合检测网络中多个层次生成的特征映射，提供了一种深度检测方法。他们为来自不同层的特征图构建快捷连接，减小其大小，并将其直接发送到最后一层，以对提取的特征进行分类。在分类时，将浅层、中层和深层采集的特征图同时输出并融合。

face取证++数据集也用于训练和测试网络。研究结果表明，新的检测方法在检测深度假视频方面击败了以前的方法。MFNN在识别低质量深度假电影方面显著提高了准确性，这通常比以前的技术更具挑战性。

Z. Xu等人（2021）提出了一种将众多视频帧的人脸作为一组来研究面部改变视频识别的视角，以及一种独特的框架集CNN （SCNN）。他们的系统提供了三个例子：t-MesoNet、t-XceptionNet和t-XceptionNet。此外，综合研究表明，该方法优于先前的技术，可以作为与其他骨干网络结合的基础。然而，他们发现一个更强大的骨干网络可以产生更好的结果。因此，寻找更好的骨干网可能是下一步正确的方法，而集约简技术对SCNN至关重要。在这种情况下，三种不同的集约简技术是直接和直观的。

此外，Kohli和Gupta（2021）提出了一种利用人脸伪造的频域特征的策略。他们的技术使用频率CNN （FCNN）来评估和分类干净和伪造的脸。为了评估FCNN的有效性，我们使用了face取证++数据集。研究表明，FCNN在实际情况下，包括这样的高、低视频质量，都能有效地检测出伪造品。此外，激活图的显示表明，FCNN学习了deepfake、Face2Face和FaceSwap操作方法的不同频率特征。在所有其他面部修改技术中，FCNN检测深度伪造的召回率最高，分别为0.9256、0.8639和0.8399，分别为raw、c23和c40。该技术还在Celeb-DF （v2）数据集以及自动facefrensic基准测试上进行了测试。研究结果证明了所建议的方法在检测面部操纵方面的有效性。

同样，Chen和Tan（2021）引入了特征转移，这是一种依赖于无监督域自适应的两阶段深度假检测方法。基于Domain-Adversarial Neural Network （BP-DANN）的反向传播（Backpropagation）用于对抗TL，比端到端对抗学习的效率更高。在预处理阶段，首先利用人脸检测网络提取视频帧的人脸区域，然后将其放大1.2倍，裁剪并保存人脸图像。CNN是在第三方提供的大规模deepfake数据集上进行训练的。最后，将人脸图像输入到CNN中，得到2048维特征向量。将收集到的特征向量存储起来，方便加载到BP-DANN中进行无监督域自适应训练（B. Xie et al ., 2023）。此外，在大型deepfake数据集上预训练的特征提取CNN可以很好地用于提取额外的可转移特征向量，在整个无监督域自适应训练中减少源和目标之间的差距。

此外，a. Yan， Yin-He等人（2021）研究了具有低质量因子的压缩深度假电影，以满足社交媒体上常见的场景。实际上，压缩视频在Instagram、微信、抖音等社交媒体平台上普遍存在。因此，确定检测压缩深度假胶片的方法成为一个关键挑战。此外，他们采用了低复杂度网络的帧级流，并对模型进行了修剪，避免了由于压缩带来的噪声而导致的噪声拟合。利用时间级流提取帧间的不一致性，发现压缩电影的时间特征。这两种流提取压缩视频的帧级和时间级信息。他们在deepfakes、FaceSwap、Face2Face、NeuralTextures和Celeb-DF数据集上测试了他们建议的两流技术，结果优于之前的工作。交叉压缩检测结果的准确性表明，他们的方法是鲁棒的压缩因素。

此外，Caldelli等人（2021）认为使用CNN的光流场差异可以区分深度伪造和真实视频。他们的研究是基于cnn的使用，这些cnn经过训练，可以利用光流场来检测视频片段时间结构中潜在的运动差异。在face取证++数据集上产生的测试结果非常有趣，并表明该功能非常适合提取假实例和真实实例之间的独特特征，特别是在处理棘手的交叉伪造场景时。此外，还演示了这种技术如何利用时间轴上的差异，当与众所周知的最先进的基于框架的方法集成时，这种差异提高了它们的效率。

L. Yan， Yin-He等（2021）提出了一种轻量级的3D CNN。CT模块的目的是在更高层次上以更少的参数提取特征。采用三维cnn作为时空模块，在时间维度上对空间信息进行融合。然而，从输入帧中收集SRM特征以抑制帧内容并强调帧纹理，从而使时空模块更好地发挥作用。此外，CT模块试图使用尽可能少的参数从时空模块的结果中提取深层特征。如前所述，3D cnn比传统的二维（2D） cnn具有更大的参数，这损害了收敛和泛化能力。CT模块旨在取代标准的3D卷积层，以尽量减少参数的数量。结果表明，他们的网络比其他网络具有更少的参数，并且在主要的深度伪造数据集上优于现有的最先进的深度伪造检测技术。由于他们的方法克服了高部署消耗的问题，同时保持了良好的检测性能，很明显，边缘设备上的深度伪造检测将很快得到应用。

此外，Mitra等人（2020）展示了一种基于dl的方法，用于检测社交媒体上的深度假视频，准确率很高。他们使用基于神经网络的技术对录像进行分类和修改。建立了一个由CNN和分类器网络组成的模型。CNN模块从XceptionNet、InceptionV3和Resnet50三种不同的结构中选择，并进行了比较研究。他们分析了现有的三个CNN模块，并决定Xception net作为最准确模型的特征提取器，以及所提出的分类器。他们使用中间压缩来训练网络，即使在高损耗的环境下也能取得很好的准确性。即使没有经过极度压缩的视频训练，他们的方法也是实现高准确性的关键。从电影中检索到的帧数决定了算法的复杂度。

Suratkar等人（2020）开发了一个使用CNN架构和TL方法检测虚假视频的系统。他们的方法是使用CNN从每个电影帧中收集特征，建立一个二元分类器，可以有效地区分真实和篡改的视频。该过程在从不同数据集中挑选的许多深度假电影上进行了测试。因此，研究结果表明，使用TL开发一个相对强大的模型进行深度假检测是可行的。有了TL的概念，所采用的技术可以使任何模型的训练速度大大提高，从而大大减少了训练时间。TL还可以更容易地将有价值的知识从一项工作转移到另一项工作，这在这种情况下得到了证明。为了获得通用性，他们的论文旨在通过组合从各种来源使用各种方法收集的数据集来获得最大的通用性。

Bonettini等人（2021）利用最新的面部操作技术解决了在视频序列中检测面部变化的困难。他们研究了几个训练有素的CNN模型的组装。在建议的方法中，通过从基础网络（EfficientNetB4）开始创建几个模型，并采用两个不同的概念：(i)注意层和（ii）连体训练。在两个包含超过119,000个视频的公开数据集上，他们证明了合并这些网络产生了有希望的人脸修改检测结果。

最后，Cozzolino等人（2019）开发了一个神经网络，增强了隐藏在视频中的模型相关痕迹，提取了一种称为视频噪声指纹的相机指纹形式，灵感来自最近的照片研究。该网络在原始胶片上使用暹罗方法进行训练，限制相同模型斑块之间的距离，并最大化不相关斑块之间的距离。实验表明，基于视频噪声指纹的系统在重要的取证任务中表现良好，包括摄像机模型识别和视频伪造定位，不需要先前的信息或微调。

但是，表6列出了使用深度学习技术的深度伪造视频应用程序及其特性。

![112722235526_01](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/112722235526_01.png)

