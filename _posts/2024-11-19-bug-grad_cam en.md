---
layout:     post
title:      "Bug Collection for Visualizing My Model Using grad_cam"
date:       2024-11-25 16:30:00
author:     "Joy Lee"
header-img: "img/in-post/bug/dog cat.jpg"
catalog: true
tags:
    - bug
    - grad_cam
    - heatmap
    - visualization
---
# Introduction

Recently, I used a cross-attention mechanism in my model and wanted to visualize the attention regions, as shown in the figure below. After some research, I found that `grad_cam` can be used for heatmap visualization. I integrated `grad_cam` into my model to visualize the cross-attention regions. During this process, I encountered various bugs, which I summarize here for future reference and to help others.

![Heatmap Visualization](https://cdn.jsdelivr.net/gh/amylee-github/my-blog-img/dog%20cat.jpg)

> For an introduction to using `grad_cam`, you can refer to the video by Bilibili creator [@Larry同学](https://space.bilibili.com/326454027) titled [【Deep Learning: Model Visualization, Feature Map Visualization, CAM Heatmap Visualization】](https://www.bilibili.com/video/BV17i421U73P/?share_source=copy_web&vd_source=9d8fb015c2e45564f35482aa604128e4&t=905). The code used in the video can be found in the comments section below the video, with a Baidu Netdisk link. This article is based on that code and adapted for my own model.

# Issue 1: Handling Multiple Input Tensors in `grad_cam`

The first issue I encountered when adapting `grad_cam` to my model was how to handle multiple input tensors. In most online examples, the `pytorch_grad_cam.GradCAMPlusPlus` class is instantiated and used with a single input tensor (usually an RGB image). However, my model requires multiple input tensors (e.g., RGB, frequency domain, and texture region images). Below, I describe my thought process and solution. If you want to skip the analysis and go straight to the solution, jump to section 3.

## 1. Passing Multiple Tensors Directly

Initially, I tried passing three tensors directly, as shown below:

```python
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
# images_b: texture region image, images_f: frequency domain image, images: RGB image
grayscale_cam = cam(images_b, images_f, images)
```

This resulted in the following error:
`TypeError: forward() missing 2 required positional arguments: 'x_f' and 'x_p'`

The error occurred because only the first tensor was passed, while the other two were not. Upon inspecting the `BaseCAM` class (from which `GradCAMPlusPlus` inherits), I found the following `forward` function:

```python
def forward(
    self, input_tensor: torch.Tensor, targets: List[torch.nn.Module], eigen_smooth: bool = False
    ) -> np.ndarray:
    input_tensor = input_tensor.to(self.device)
```

This function only accepts a single `input_tensor`. However, my model's `forward` function requires three tensors:

```python
def forward(self, x_b, x_f, x_p): # x_b: texture region, x_f: frequency domain, x_p: RGB
```

Hence, the error occurred.

## 2. Using a Tuple to Combine Tensors

Next, I tried combining the three tensors into a tuple and passing it as a single argument:

```python
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
grayscale_cam = cam((images_b, images_f, images)) # Combine tensors into a tuple
```

This resulted in another error:
`AttributeError: 'tuple' object has no attribute 'to'`

The error occurred because the `forward` function attempts to call `.to(self.device)` on the input, which is only valid for tensors, not tuples. Additionally, the function explicitly specifies `input_tensor: torch.Tensor`, meaning it only accepts a single tensor.

## 3. ⭐ Concatenating Tensors into a Single Tensor

The final solution was to concatenate the tensors into a single tensor. This approach satisfies `grad_cam`'s requirement for a single tensor input. The necessary code modifications are as follows:

### (1) Modifications to `grad_cam`:

```python
# Concatenate tensors along the channel dimension and move to GPU
images_all = torch.cat((images_b, images_f, images), dim=1).cuda()
cam = pytorch_grad_cam.GradCAMPlusPlus(model=model, target_layers=target_layer)
grayscale_cam = cam(images_all)
```

### (2) Modifications to the Model's `forward` Function:

```python
def forward(self, x): # Accept a single tensor
    x_b = x[:, :3, :, :] # Extract the first 3 channels
    x_f = x[:, 3:6, :, :] # Extract channels 4-6
    x_p = x[:, -3:, :, :] # Extract the last 3 channels
```

If the number of channels differs, adjust accordingly. The key idea is to concatenate the tensors into one and then split them back inside the model. This resolves the issue of `grad_cam` requiring a single tensor input.

Finally, the first issue was resolved! 😭

> Note: This method requires modifying the model's code. After visualization, you may need to revert the changes. However, this straightforward approach allowed me to move past this bug.

## P.S.: Alternative Solutions

**1.** While searching for solutions, I found a suggestion in the [grad_cam GitHub repository](https://github.com/jacobgil/pytorch-grad-cam) under [this issue](https://github.com/jacobgil/pytorch-grad-cam/issues/279#issuecomment-1221199929). I didn't explore it in detail but drew inspiration from it.

**2.** The repository's author also provided a solution in [this issue](https://github.com/jacobgil/pytorch-grad-cam/issues/406#issuecomment-1500393283). I couldn't get it to work, but others might find it helpful.

# Issue 2: Error with `show_cam_on_image`

## 1. Detailed Problem Description

After obtaining `grayscale_cam` using `pytorch_grad_cam.GradCAMPlusPlus`, I attempted to overlay it on the original image using `show_cam_on_image`:

```python
visualization_img = show_cam_on_image(src_img, grayscale_cam, use_rgb=False)
```

However, this resulted in the following error:
`cv2.error: OpenCV(4.9.0) /io/opencv/modules/imgproc/src/colormap.cpp:736: error: (-5:Bad argument) cv::ColorMap only supports source images of type CV_8UC1 or CV_8UC3 in function 'operator()'`

The error occurred in the first line of the `show_cam_on_image` function:

```python
heatmap = cv2.applyColorMap(np.uint8(255 * mask), colormap)
```

The error indicates that `applyColorMap` only supports images of type `CV_8UC1` (single-channel 8-bit unsigned integer) or `CV_8UC3` (three-channel 8-bit unsigned integer).

## 2. Problem Analysis

Upon inspecting `src_img`, I found that it contained negative values, which are incompatible with `applyColorMap`. This issue arose because I mistakenly applied `transforms.Normalize` to `src_img`, which is unnecessary for visualization.

## 3. Solution

Ensure that `src_img` follows the correct transformation pipeline: `origin_img → rgb_img → crop_img → canvas_img → src_img`. Specifically, avoid applying `transforms.Normalize` to `src_img`.

If processing multiple images, use a loop:

```python
src_img = np.float32(canvas_img) / 255
cam_img = []
for i in range(src_img.shape[0]):
    cam_img.append(show_cam_on_image(src_img[i], grayscale_cam[i], use_rgb=False))
cam_img = np.stack(cam_img)
```

This resolved the second issue! 🎉

# Conclusion

These are the main issues I encountered while using `grad_cam` for visualization. Although the process was challenging, the final results were rewarding. I hope this post helps others facing similar issues. Feedback and corrections are welcome! 🤗
