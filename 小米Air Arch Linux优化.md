# 小米Air安装Arch Linux之图形界面配置（Gnome 和 sway）
## 0. 前言
[上一篇文章](http://www.cnblogs.com/fatshen/p/7631371.html)简单讲述了在小米Air上安装Arch Linux的经验，但是安装完后基本系统后，还需要额外的配置才能进到日常使用。下文简单列举一些步骤。
## 1. 参考网站
主要还是参考[ARCH WIKI](https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。具体参考链接会放在对应的地方。
## 1. 基本配置
主要参考WIKI中的[General recommendations (简体中文)](https://wiki.archlinux.org/index.php/General_recommendations_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))和va_chester的[Archlinux安装和使用技巧](http://www.cnblogs.com/vachester/p/5635819.html)，这一部分主要根据记忆去。
### 1.1 用户和用户组

```shell
pacman -S zsh   #安装zsh
useradd -m -g users -G wheel -s /bin/zsh sandwich   #新建用户
passwd sandwich   #设置密码
```
-g为 用户所属组，-G用户附加组，wheel组是类似于管理员组。

### 1.2 sudo

```shell
pacman -S sudo  #安装sudo
```
用visudo来添加

```shell
%wheel      ALL=(ALL) NOPASSWD: ALL
```
##2. 额外配置
### Gnome安装

```shell
pacman -S gnome #安装gnome
systemctl enable gdm.service    #gdm开机运行
```

我们先选择安装Gnome这个大家较为熟悉的桌面环境，Gnome默认会安装gdm。Gnome属于桌面环境，gdm属于窗口管理器。一个窗口管理器可以管理多个桌面环境，为下文我们安装sway桌面作准备。

如果使用的是笔记本，有些Gnome配置中的Settings-Devices-Mouse & Touchpad中建议打开Tap to click，点击触摸板即可点击。

### NetworkManager

[NetworkManager (简体中文)](https://wiki.archlinux.org/index.php/NetworkManager_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
使用wpa_suppliant太麻烦了，不如试下更加方便的networkmanager来管理网络，具体使用方法这里就不再赘述。

```shell
pacman -Syu networkmanager
systemctl start NetworkManager
systemctl enable NetworkManager
```

###zsh 和 oh-my-zsh

[终极 Shell——ZSH](https://zhuanlan.zhihu.com/mactalk/19556676)
[Ubuntu 下安装oh-my-zsh](http://www.jianshu.com/p/9a5c4cb0452d)

上面两篇文章已经比较清楚地说明了安装zsh和oh-my-zsh的方法。如果在gnome terminal中显示出现异常，请更改terminal的配置。在Edit-Profile Preference-Colors中，取消选择Use colors from system theme。

###shadowsocks
[Shadowsocks (简体中文)](https://wiki.archlinux.org/index.php/Shadowsocks_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
安装shadowsocks和shadowsocks-libev都可以实现类似的功能。不过我还是更加推荐使用shadowsocks，因为它的systemd服务可以调用/etc/shadowsocks/里的不配置。以调用/etc/shadowsocks/foo.json为例：

```shell
pacman -S shadowsocks   #安装ss
vim /etc/shadowsocks/foo.json   #配置你的ss
systemctl start shadowsocks@foo #启动ss
systemctl enable shadowsocks@foo    #开机启动
```
需要进行[chrome配置](https://github.com/FelisCatus/SwitchyOmega/wiki/GFWList)才能直接地科学上网。

###中文显示

[Fonts (简体中文)](https://wiki.archlinux.org/index.php/Fonts_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))

安装思源宋体

```shell
pacman -S adobe-source-han-sans-cn-fonts
pacman -S adobe-source-han-serif-cn-fonts
fc-cache-fv
```

###中文输入法

```shell
pacman -S ibus-rime ibus-table ibus-qt
```
[ibus](https://wiki.archlinux.org/index.php/IBus_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))为输入法框架，ibus-qt是QT应用程序支持，ibus-rime才是输入法。

在Settings-Region & Language-Input Source中加入China(Rime)。

```shell
ibus-setup #进行ibus设置
```

Ctrl-~可以切换输入法。

###bumblebee

```shell
pacman -S bumblebee
pacman -S mesa
pacman -S nvdia
pacman -S xf86-video-intel
gpasswd -a user bumblebee
systemctrl enable bumblebee.d.service
pacman -S mesa-demos
optirun glxgears -info
pacman -S bbswitch
```

这一步很关键，小米Air有独卡和集成显卡，刚安装完Arch桌面系统，会发电源使用得飞快，使用[Bumblebee](https://wiki.archlinux.org/index.php/Bumblebee_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))和[bbswitch](https://github.com/Bumblebee-Project/bbswitch)可以实现禁用Nvidia显卡，达到Arch省电的目的。

###yaourt

```shell
pacman -S yaourt
```

安装[yaourt](https://wiki.archlinux.org/index.php/AUR_helpers_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#yaourt)可以让用户安装[AUR](https://wiki.archlinux.org/index.php/AUR_helpers_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))的软件。

###urxvt

```shell
pacman -S rxvt-unicode #安装urxvt
urxvt --help 2>&1 | grep scroll #查看scroll配置
```

urxvt是一个可高度定制的终端，下面直接贴配置了.Xdefaults。

[awesome-urxvt](https://github.com/bookercodes/awesome-urxvt)可以查看更多urxvt的信息。

```shell
Xcursor.theme: DMZ-White
! URxvt.font: xft:monospace:size=14
URxvt*font: xft:Source\ Code\ Pro\ Medium:pixelsize=18:antialias=true:hinting=true,xft:Source\ Code\ Pro\ Medium:pixelsize=18:antialias=true:hinting=true

*background: #232C31
*foreground: #C5C8C6
! black
*color0: #2D3C46
*color8: #425059
! red
*color1: #A54242
*color9: #CC6666
! green
*color2: #8C9440
*color10: #B5BD68
! yellow
*color3: #DE935F
*color11: #F0C674
! blue
*color4: #5F819D
*color12: #81A2BE
! magenta
*color5: #85678F
*color13: #B294BB
! cyan
*color6: #5E8D87
*color14: #8ABEB7
! white
*color7: #6C7A80
*color15: #C5C8C6

URxvt.internalBorder:      0
URxvt.externalBorder:      0

URxvt.scrollBar:           false

URxvt*fading:              30

! URxvt.perl-lib: /usr/lib/urxvt/perl
urxvt*perl-ext-common:	fullscreen
urxvt*keysym.F11: 	perl:fullscreen:switch

```

## 2. Sway



##3. Todo

###指纹识别
###外接显示器
###telegram
###tmux
###ssd

### markdown

### sublime







