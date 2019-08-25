---
layout: post
title: "Manjaro-Gnome美化调优"
date: 2019-08-16 
description: "Linux Arch，Manjaro Gnome，Gnome美化"

tag: Manjaro 
---  



## 一、安装插件

在谷歌扩展商店搜索gnome-shell extrnsion（管理Manjaro扩展的插件）

## 二、美化

### 主题

Arc主题+copernico（透明的shell-theme）

安装

```bash
yay -S gtk-theme-arc-git
yay -S gnome-shell-theme-copernico-git
```

然后在tweak中选择这两个主题，再去网上找一张好看的壁纸，最后在dash to dock中配置一下（选择在下方并设置透明），就可以了

### 扩展

（可以在Gnome Shell Extensions上管理安装的扩展，也可以在Tweak Tool（优化）里面管理扩展和本地的个性化设置）

1、Dash to Dock（系统自带）

调整桌面图标位置，设置透明度

2、Coverflow Alt-Tab 
 alt-tab 切换效果美化

3、GnomeStatsPro
 系统监视器，显示电脑CPU状况

4、simple net speed
 显示实时网速

5、Status Area Horizontal Spacing
 使顶栏更加紧凑

6、(K)StatusNotifierItem/AppIndicator Support（系统自带）
 可以在顶栏显示后台程序图标

7、Pamac Updates Indicator（系统自带）
 提醒软件更新状态

8、User Themes (系统自带)
 让用户可以使用shell主题

## 三、调优

1、设置快捷键：

启动终端：打开设置，设备中选择keyboard，添加快捷键

名称：terminal

命令：gnome-terminal

快捷键：Ctrl+Alt+T（这个可以按自己习惯设置）

打开文件资源管理器：（同上）

名称：文件管理器

命令：nautilus

快捷键：super+E（super键就是win键）

2、大号字体

设置->通用辅助功能 开启 大号文本

3、透明终端

manjaro默认的终端是不支持设置透明的，所以需要重新安装一个终端

直接在软件管理里面，搜索gnome-terminal-fedora安装就可以了（可能需要重启电脑）