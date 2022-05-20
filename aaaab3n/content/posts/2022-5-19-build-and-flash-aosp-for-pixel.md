---
layout: post
title: "Build and Flash AOSP 9 for Pixel XL (marlin)"
date: 2022-05-19  10:02:59 +0800
category: Technology
tags: 
    - Android 
    - Pixel XL 
    - AOSP
---

## 前言
最近在打安卓隐私相关的工，所以需要编译一个 AOSP. 之前高中的时候觉得新奇而买的 Pixel XL 就派上了用处。

## 下载 AOSP 源码

因为 AOSP 源码极大，所以使用 [USTC](https://lug.ustc.edu.cn/wiki/mirrors/help/aosp/) 的镜像源来加速

基本上和 [初始同步方法 2](https://lug.ustc.edu.cn/wiki/mirrors/help/aosp/) 一样。要注意的是，其依旧会向 Google 请求数据，所以需要接入互联网。

这里采用的版本是 `PQ3A.190801.002 |	android-9.0.0_r46 |	Pie |	Pixel 3 XL, Pixel 3, Pixel 2 XL, Pixel 2, Pixel XL, Pixel |	2019-08-01`

```bash
$ mkdir -p AOSP
$ cd AOSP
$ repo init -u git://mirrors.ustc.edu.cn/aosp/platform/manifest -b android-9.0.0_r46
$ repo sync --jobs=10
```

![](https://snz04pap002files.storage.live.com/y4mHgTjrXnZLsSP-5V7p4gl04oT2rmZ8JQVr16eOO9Do16QYGKdl7UAJaI6P_YEF-YLcYMb1AprfCvJwJQRWY2sJeXyO0P5Kt1BZCnNDAIi4El3p8UBucstR7kdgmjxme7UPDpsb2naZAygMQ0xWdvQujlEFf6Gu96ZlK3Dydgpmp9Em64VWUfeCuUHZjou6b-M?width=256&height=78&cropmode=none)

## 下载 Driver

打开 [Pixel XL binaries for Android 9.0.0 (PQ3A.190801.002)(https://developers.google.cn/android/drivers#marlinpq3a.190801.002) 下载并解压到 AOSP 源码的根目录并运行

```bash
~/AOSP$ wget https://dl.google.com/dl/android/aosp/google_devices-marlin-pq3a.190801.002-9ce9ce62.tgz
~/AOSP$ tar -xzvf ./google_devices-marlin-pq3a.190801.002-9ce9ce62.tgz
~/AOSP$ ./extract-google_devices-marlin.sh
# Press q and Enter I ACCEPT to continue
# vendor/
# vendor/google_devices/
# vendor/google_devices/marlin/
# vendor/google_devices/marlin/device-vendor-marlin.mk
# vendor/google_devices/marlin/BoardConfigPartial.mk
# vendor/google_devices/marlin/device-partial.mk
# vendor/google_devices/marlin/proprietary/
# vendor/google_devices/marlin/proprietary/vendor.img
# vendor/google_devices/marlin/android-info.txt
# vendor/google_devices/marlin/BoardConfigVendor.mk
```

```bash
~/AOSP$ wget https://dl.google.com/dl/android/aosp/qcom-marlin-qp1a.190711.019-4394281d.tgz
~/AOSP$ tar -xzvf ./qcom-marlin-qp1a.190711.019-4394281d.tgz
~/AOSP$ ./extract-qcom-marlin.sh
# Type "I ACCEPT" if you agree to the terms of the license: I ACCEPT

# vendor/
# vendor/qcom/
# vendor/qcom/marlin/
# vendor/qcom/marlin/BoardConfigPartial.mk
# vendor/qcom/marlin/device-partial.mk
# vendor/qcom/marlin/proprietary/
# vendor/qcom/marlin/proprietary/lib64/
# vendor/qcom/marlin/proprietary/lib64/vendor.qti.qcril.am@1.0.so
# vendor/qcom/marlin/proprietary/lib64/vendor.qti.atcmdfwd@1.0.so
# vendor/qcom/marlin/proprietary/qcrilhook.jar
# vendor/qcom/marlin/proprietary/VZW_profiles.xml
# vendor/qcom/marlin/proprietary/ROW_profiles.xml
# vendor/qcom/marlin/proprietary/ATT_profiles.xml
# vendor/qcom/marlin/proprietary/pktlogconf
# vendor/google_devices/
# vendor/google_devices/marlin/
# vendor/google_devices/marlin/device-vendor-marlin.mk
# vendor/google_devices/marlin/android-info.txt
# vendor/google_devices/marlin/BoardConfigVendor.mk
```

## 开始编译
```bash
$ source build/envsetup.sh
$ lunch
45
# 45. aosp_marlin-userdebug
# ============================================
# PLATFORM_VERSION_CODENAME=REL
# PLATFORM_VERSION=9
# TARGET_PRODUCT=aosp_marlin
# TARGET_BUILD_VARIANT=userdebug
# TARGET_BUILD_TYPE=release
# TARGET_ARCH=arm64
# TARGET_ARCH_VARIANT=armv8-a
# TARGET_CPU_VARIANT=kryo
# TARGET_2ND_ARCH=arm
# TARGET_2ND_ARCH_VARIANT=armv8-a
# TARGET_2ND_CPU_VARIANT=kryo
# HOST_ARCH=x86_64
# HOST_2ND_ARCH=x86
# HOST_OS=linux
# HOST_OS_EXTRA=Linux-5.4.148-x86_64-Ubuntu-20.04.4-LTS
# HOST_CROSS_OS=windows
# HOST_CROSS_ARCH=x86
# HOST_CROSS_2ND_ARCH=x86_64
# HOST_BUILD_TYPE=release
# BUILD_ID=PQ3A.190801.002
# OUT_DIR=out
# ============================================

$ make -j $(nproc)
```
![](https://snz04pap002files.storage.live.com/y4mXiJffkPzom1v43loKC2oERW1RZ6XBlcv_oq-1OeCkJDiebcxXTivNz-G1rTKnxh-fEMmByob4EgX0mBeuUfRO3LRxSnS4te1G-rNvyeW4_8_KF8Cj9M4x0bNbMhP9NHh3PAPxHpSef_6VYorTiCeYY2yWKWtUWlPVG0QGfsjmrkhPthXVVJ6QR-5OrPdnUV6?width=1024&height=551&cropmode=none)

```bash
#### build completed successfully (10:07 (mm:ss)) ####
```

## 编译成功之后，打包 img 文件
```bash
$ cd out/target/product/marlin  # 这是输出文件夹
$ tar -zcvf malin-9.tar.gz .
```
![](https://snz04pap002files.storage.live.com/y4mtK5Oiyjq2rFZCdYEcHDvO5AE8kfgEmId0t47V7hlZn8el1eH98nZaXsBIoue8w-RzLW7FXUsA4yYjTdP1Ucg1E7VZZy-39L7s-o_y83tF_n71SczXvp7HkJUxm-D7kdhZMEKKO_b3Eb1Eovwt4BXGD4QJAcq5KXF4XEz60ckp00H2r-gZRNMEhNzqsHOvND8?width=660&height=46&cropmode=none)

压缩的话不一定需要压缩这么多东西,  `tar -zcvf malin-9.tar.gz ./*.img` 再加一些必要文件可能就可以了。实测下来压缩时间非常长，如果硬要全部打包可以用 `pigz`.

## 在 Windows 下刷机

用 `choco install adb` 或者其它方式安装 `android debug tools`

1. 重启至 `fastboot`
2. 解压 `malin-9.tar.gz`
3. `$Env:ANDROID_PRODUCT_OUT = "C:\Users\Nyove\Desktop"`
4. ` fastboot flashall -w`

![](https://snz04pap002files.storage.live.com/y4m3ukARD_CXxnCjpaH0Fop5Kug5fBh-5J5gg-JJbt2-rmOY9v29MCd7yVWc6HZKizjKXS_XhFmLZOL3B0SjMKrm9AorU5SVm5QHxAU7OY-X9jB6POWohR6MxACwJAgjapJIdsNybFWcIqMCkanyjF411dmFUoiR3JU9zML-JxJLtxdFDH-zyqv2IycfhBWUI0N?width=1024&height=352&cropmode=none)

![](https://snz04pap002files.storage.live.com/y4m7S2RH82rdS5nBWxk_U1zAqI-lXXHCjVPxi0xM-0ChhieSEIjbFxqkrT8cXT-p0v1DNzh36lXNwVnPh7WYD631yc2-q_x1dZmrBR8FRT5S3ymug0QBcZ7D4uy18NYO0pJcklWv7G9BIHjd7ErYyS63ZNSU_TgWyXxNtlLNNWpvDF3menDFGvunZsOT8qWKBun?width=768&height=1024&cropmode=none)
## 参考资料
- [记录AOSP源码编译刷机(pixel 4a)](https://codeantenna.com/a/u4LA7IZ7JU)