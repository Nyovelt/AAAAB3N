---
layout: post
title: "Magisk 25.2 通过基本 SafetyNet 测试方法"
date: 2022-08-05  10:03:59 +0800
category: Technology
tags: 
    - Magisk
---

# Magisk 25.2 通过基本 SafetyNet 测试方法

自从 John Wu 被谷歌招安了之后，谷歌针对 Magisk 的机制做了修复。但道高一尺魔高一丈，对于基本的 Safety Net Check 还是有办法绕过的。

## 安装 Magisk

1. 下载 `magisk.apk`
2. 重命名为 `magisk.zip`
3. 在 recovery 刷入 `magisk.zip`
4. 安装 `magisk.apk`

## 隐藏 Magisk

1. 主页 - 右上角 - 设置
2. 以其它包名安装 Magisk

## Zygisk

1. 主页 - 右上角 - 设置
2. 打开 "在 Zygisk 中运行 Magisk"
3. 将要针对的东西配制到排除列表
   1. google 家的所有东西
   2. Ingress

## 安装模块

1. MagiskHide Props Config
2. Universal SafetyNet Fix

## 效果

1. Ingress 可以正常游戏
2. Google Pay 无法工作
