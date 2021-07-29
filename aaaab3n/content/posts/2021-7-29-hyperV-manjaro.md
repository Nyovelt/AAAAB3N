---
layout: post
title: "【Hyper-V】Manjaro xfce"
date: 2021-07-29  13:02:59 +0800
category: Technology
tags: hyper-v manjaro linux
---

# 0x00 
本文记录了我在 Hyper-V 中安装 Manjaro 作为 Linux 环境。 Manjaro 相比 Ubuntu, Debian 有更方便的包， 相比 Arch 和 Nix 更加方便。装在 Hyper-V 中的另外一个优势是，当完成了基础环境的配置之后可以批量复制，创建不同功能的虚拟机。

# 0x01 Hyper-V
- Download [here](https://manjaro.org/downloads/official/xfce/)
- Gen 2
![](https://snz04pap002files.storage.live.com/y4m4jiZeuSigRcfJRAVIpHW-zlowhoIeT1K7rCf9YoKa5jMVKe-efSqHL8FW-29-mJqJy_sJSqkrsNJb-kKiWFfM7tYj79xroTOLpFAyZXgt2QejXwT1-lCqcRrdAyUu9On58E5-d2Ktjqf-jtLq6Gfyr1XrcU6VFq7pZSqtX6Ng91Nq_woeHbx5lCMMFNhShLT?width=1024&height=633&cropmode=none)
- uncheck `Use Dynamic Memory for this virtual machine`
- `Secure Boot` off
- load `.iso` 
- Allow enhanced session mode
- `64G` VHD recommend

# 0x02 Boot
- ` CTRL-ALT-F2 ` to enter command line
- `manjaro` `manjaro` to login
- 启动X Window
```
su
pacman –Sy 
pacman –S xf86-video-fbdev
systemctl restart lightdm
```
- Then you can see the GUI

# 0x03 Install
Follow the Tips and `no Chinese`

# 0x04 Reboot 
- Pull off `.iso`
- Repear `0x03`

# 0x05 个性化配置
## 升级
```
sudo pacman -Syu
```
## archlinuxcn
See [here](https://sjtug.org/post/mirror-help/archlinux-cn/)
## enable ssh
```
sudo systemctl enable sshd
sudo systemctl start sshd
```
## AUR
```
sudo pacman -S base-devel 
# Install yay from git
cd /tmp
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd ..
# clean /tmp folder
rm -rf yay
```
## nano 
```
yay -S nano
```
## su without passward
```
sudo viduo
```
uncomment the line with `:NOPASSWD`
## xrdp
```
cd /tmp
sudo git clone git@github.com:microsoft/linux-vm-tools.git
cd linux-vm-tools/arch
./makepkg.sh
sudo ./install-config.sh
```
## 编辑.xinitrc
- change `local dbus_args=(--sh-syntax --exit-with-session)` to `local dbus_args=(--sh-syntax)`
- 执行完毕之后，就把虚拟机关机，然后以管理员身份运行Power Shell，然后执行下面的命令，注意把斜体部分替换成你的虚拟机的真实的名字。

    -  `Set-VM -VMName _Your_Manjaro_VM_ -EnhancedSessionTransportType HvSocket`

## dotfile
```
git@github.com:Nyovelt/dotfile.git
```

## shell
- Install `oh-my-zsh` and config themes and plugins
- Install `starship` and config
```
mkdir -p ~/.config && touch ~/.config/starship.toml
```

# Thanks
- [https://medium.com/@iceboundrock/%E5%9C%A8hyper-v%E9%87%8C%E5%AE%89%E8%A3%85manjaro-kde-1bdf810dbc10](https://medium.com/@iceboundrock/%E5%9C%A8hyper-v%E9%87%8C%E5%AE%89%E8%A3%85manjaro-kde-1bdf810dbc10)
- [https://zhuanlan.zhihu.com/p/77676057](https://zhuanlan.zhihu.com/p/77676057)
- [https://github.com/Jguer/yay/issues/1087](https://github.com/Jguer/yay/issues/1087)
- [https://gist.github.com/knrt10/a9c3277f7a13263a9b9b1da9ada3694f](https://gist.github.com/knrt10/a9c3277f7a13263a9b9b1da9ada3694f)