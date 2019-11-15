---
layout: post
title: "Manjaro-Gnome美化调优"
date: 2019-08-16 
description: "Linux Arch，Manjaro Gnome，Gnome美化"
tag: Manjaro 
---

# Manjaro-Gnome 美化调优

## 1. 安装插件

在谷歌扩展商店搜索gnome-shell extrnsion（管理Manjaro扩展的插件）

## 2. 美化

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

1. Dash to Dock（系统自带）

调整桌面图标位置，设置透明度

2. Coverflow Alt-Tab 
    alt-tab 切换效果美化

3. GnomeStatsPro
    系统监视器，显示电脑CPU状况

4. simple net speed
    显示实时网速

5. Status Area Horizontal Spacing
    使顶栏更加紧凑

6. TopIcons Redux（系统自带）
   

（推荐使用：Tray Icons）可以在顶栏显示后台程序图标
    
7. Pamac Updates Indicator（系统自带）
    提醒软件更新状态

8. User Themes (系统自带)
    让用户可以使用shell主题

## 3. 调优

### 3.1 设置快捷键

启动终端：打开设置，设备中选择keyboard，添加快捷键

名称：terminal

命令：gnome-terminal

快捷键：Ctrl+Alt+T（这个可以按自己习惯设置）

打开文件资源管理器：（同上）

名称：文件管理器

命令：nautilus

快捷键：super+E（super键就是win键）

### 3.2 大号字体

设置->通用辅助功能 开启 大号文本

### 3.3 透明终端

manjaro默认的终端是不支持设置透明的，所以需要重新安装一个终端

直接在软件管理里面，搜索gnome-terminal-fedora安装就可以了（可能需要重启电脑）

## 4. BUGS

### 4.1 2019-09-07 更新出错

```bash
yay -Syyu
```

QQ的顶部图标消失，以为是数据源没有更新完，就又更新了一下

```bash
yay -Syyu
#数据源更新失败
```

what？？？

随后一想应该是gnome扩展跟数据源起冲突了，就在gnome shell extensions插件里面看到，我用的Arc主题Arc Menu果然需要更新，点击更新，QQ顶部图标出现，终端刷新数据源正常

PS：QQ图标显示时行时不行，推测是扩展的问题，就把TopIcons Redux删掉，换成Tray Icons了

### 4.2 2019-09-26 更新出错

```shell
#日常更新
yay -Syyu
#报错如下
无法满足依赖关系:
安装jre (13-1) 破坏了依赖关系， 'jre<13' 被 jdk 所需要

#解决办法
#在软件管理器中，搜索jdk，删除jdk-12.0
yay -Syyu
#此时可以更新成功
#最后在系在jdk-13就可以了
#其他版本冲突也是类似的操作
```

### 4.3 MySQL 异常

异常一：

```shell
#初始化失败
sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql

#上面初始化失败,提示libicu63没找到:
$ mysqld: error while loading shared libraries: libicuuc.so.63: cannot open shared object file: No such file or directory

# 进入/usr/lib/icu,发现只有64版本

# 安装icu63
$ yay -S icu63

# 等待缓慢的下载和安装
# 完成后再看/usr/lib/icu,发现已经有icu63了

# 再次初始化
$ sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql

# 记住临时密码登录,或者修改配置文件免密登录,后者修改/etc/mysql/my.cnf,在最后一行加上
skip-grant-tables

$ sudo systemctl restart mysqld

# 如果又出现初始化失败的问题,重装即可

$ mysql -u root -p
# 不用密码[回车]即可
```

异常二：

```bash
#启动mysql时报错
Job for mysqld.service failed because the process exited with error code.See "systemctl status mysqld.service" and "journalctl -xe" for details
```

一看这个就完全懵比，这是个啥错误

终端输入：

```bash
ldd /usr/bin/mysqld
#查看mysql的依赖是否都存在
#发现下面有“not found”
libevent_core-2.1.so.6 => not found
```

这是因为依赖版本更新比较快，已经到libevent_core-2.1.so.7了，但是mysql跟新比较慢

```bash
#查看现有的依赖
cd /usr/lib
ls
#发现已经到libevent_core-2.1.so.7
#然后直接复制7为6就可以了
cp libevent_core-2.1.so.7 libevent_core-2.1.so.6
#之后就继续你的步骤就可以了
```

### 4.4 2019-10-12 更新出错

```shell
yay -Syyu
#错误：无法从 mirrors.ustc.edu.cn : Operation timed out after 10001 milliseconds with 0 out of 0 bytes received 获取文件 'archlinuxcn.db'
#错误：无法升级 archlinuxcn (下载数据库出错)
# arch4edu                 217.7 KiB   429K/s 00:01 [######################] 100%
#错误：同步所有数据库失败
#Error installing repo packages

#更换源
sudo pacman-mirrors -i -c China -m rank
sudo yay -Syyu
#完美解决
```

### 4.5 2019-10-16 更新出错

```shell
#更上次的大更新错误如出一辙
#所以就需要我们每次大更新之前，进行换源
sudo pacman-mirrors -i -c China -m rank
#选择速度快的就行，一般是ustc和sjtu
```

## 5. 常用命令

```shell
#查看ip
ip addr
ip route
```

