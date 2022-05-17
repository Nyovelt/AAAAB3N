---
layout: post
title: "Install OpenCV on Windows"
date: 2022-05-17  10:02:59 +0800
category: Technology
tags: Windows 
---

# Install OpenCV on Windows 在 Windows 系统上安装 OpenCV 

This article records how to install opencv on windows with `choco` and use opencv in Visual Studio.

## Install OpenCV

```shell
choco install opencv
```

My version is OpenCV 4.5.5, my installation path is `C:/tools`

Add PATH:
![](https://i.imgur.com/hZHCwGd.png)

## Use in Visual Studio 

Reference: [How to build applications with OpenCV inside the "Microsoft Visual Studio" ](https://docs.opencv.org/4.x/dd/d6e/tutorial_windows_visual_studio_opencv.html)

### Add PATH
![](https://i.imgur.com/fc5I0Zp.png)

If `opencv_world455d.lib` not found, probably you miss this step.

Ref: [SOLVED: opencv_world451.dll was not found](https://www.youtube.com/watch?v=tZ4ypUDtFX4)

## Add Library and Includes
![](https://i.imgur.com/ZvQ33u5.png)

## Set Additionary Library 

In OpenCV 4.5.5, it should be `opencv_world455d.lib` or `opencv_world455.lib` if you want debug mode off.

It is the name of a file in `lib` folder.

![](https://i.imgur.com/CMpcMaF.png)

## Test

![](https://i.imgur.com/fxGxoDh.png)

The code is from [Adding OpenCV 4.1.2 to Visual Studio 2019 Project in Windows using Pre-built binaries](https://subwaymatch.medium.com/opencv-410-with-vs-2019-3d0bc0c81d96)
