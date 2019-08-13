---
layout: post
title: Manjaro Gnome 安装及其简单配置
date: 2019-08-10
author: Serendipity-zsh
head-img: img/3.jpg
catelog: true
tags:
    -ArchLinux
    -Manjaro
---





# Manjaro gnome 安装及其简单配置

## 1、配置相关源

### 排列源

```bash
sudo pacman-mirrors -g
```

寻求一步到位的可以选择这个命令

```bash
sudo pacman-mirrors -c China
```

### 同步

```bash
sudo pacman-optimize && sync 
```

### 升级系统

```bash
sudo pacman -Syyu
```

#### 设置更新源

```bash
sudo pacman-mirrors -gb testing -c China
```

#### 安装aria2并加速源下载

```bash
sudo pacman -S aria2
```


但是如果你在安装时候选择了中文，就不用去配置软件源。因为已经自动配置。此后可以配置archlinuxcn源，便于我们安装一些软件例如`Google-Chrome`等。

#### 切换中国源

```bash
sudo gedit /etc/pacman.conf
```

如果提示没有gedit , 则执行命令 :

```bash
sudo pacman -S gedit
```

修改/etc/pacman.conf`文件 在文件后添加如下语句

> [archlinuxcn]
> SigLevel = Optional TrustedOnly
> Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
> [arch4edu]
> SigLevel = Never
> Server = http://mirrors.tuna.tsinghua.edu.cn/arch4edu/$arch

```bash
gedit /etc/pacman-mirrors.conf
```

修改如下地方为中国：

```bash
OnlyCountry = China
```

#### 更新源列表

```bash
sudo pacman -Syyu
```

### 安装archlinuxcn-keyring

```bash
sudo pacman -S archlinuxcn-keyring
```

如果安装失败,则执行以下命令:

```bash
 sudo pacman -Syu haveged

 sudo systemctl start haveged

 sudo systemctl enable haveged

 sudo rm -rf /etc/pacman.d/gnupg

 sudo pacman-key --init

 sudo pacman-key --populate archlinux

 sudo pacman -S archlinuxcn-keyring

 sudo pacman-key --populate archlinuxcn
```

#### 安装yaourt以及pacaur

```
sudo pacman -S yaourt pacaur
复制代码
```


导入GPG Key

```
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
复制代码
```

此后就可以去安装自己想要使用软件啦！！


**刷新缓存**

```bash
sudo pacman -Syy
```

**添加ArchLinuxCN的源**

```bash
sudo gedit /etc/pacman.conf
```

（由于我这里用的是GNOME桌面，因此自带的编译器是gedit。你可以安装vim或者用其他的编译器）

在打开的文件末尾添加如下内容：2、安装中文输入法

> [archlinuxcn]
> SigLevel = Optional TrustedOnly
> Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

然后执行下面的命令：

> ```bash
> sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
> ```

这一步是为了更新缓存以及导入密钥链。

**注意**，你在执行更新命令时可能会报错，这时你只需再执行一遍即可。我也不知道这是什么神仙bug。

这一条命令`sudo pacman -Syy`是用来刷新更新缓存的，有事没事的时候都可以刷两下！建议每次打开终端的时候都可以刷下！

**最后，你需要更新一下系统**。Manjaro是滚动更新的，这意味着你在官网下载的系统iso并不是最新的。而且每过一段时间系统都会有一定幅度的更新。终端输入：

```bash
sudo pacman -Syyu
```

这个命令不仅是更新系统，而且也更新软件包。可以看到更换为国内源之后下载速度非常之快，我的可以达到7m/s。

如果你要安装某个软件，终端输入

```bash
sudo pacman -S [name]
```

最后重启系统。至此Manjaro的配置和更新算是完成了。

**解决双系统时差相差8小时**

```bash
sudo timedatectl set-local-rtc true
```

然后再重启manjaro系统就可以了

# 2、主题

找自己喜欢的主题就可以了

https://linux.cn/article-10720-1.html

# 3、安装常用的工具

#### （1）、安装Yay

```bash
sudo pacman -S yay
```

使用yay 更改pacman配置`/etc/pacman.conf 打开Color`

- **搜索：**yay -Ss 
- **安装：** yay -S 

安装了Yay之后就很舒服了

之后下载安装软件就可以直接：

```bash
yay -S software
```

搜索软件：

```bash
yay -Ss software
```



#### （2）、安装fcitx用来管理输入法

安装fcitx

```bash
sudo pacman -S fcitx fcitx-im fcitxconfigtool
或者
yay -S fcitx fcitx-im fcitxconfigtool
```

fcitx为基础安装包，fcitx-im 用于GTK/QT等界面上使用的包，fcitx-configtool为配置界面（kde下还能装一个kde版的configtool）

#### （3）、安装搜狗输入法

```bash
yay -S fcitx-sogoupinyin
```

#### （4）、QQ & Tim

安装QQ：

```bash
sudo pacman -S deepin.com.qq.im
或者 yay -S deepin.com.qq.im
```

安装Tim：

```bash
sudo pacman -S deepin.com.qq.office
或者 yay -S deepin.com.qq.office
```

#### （5）、WPS

虽然Manjaro自带Microsoft Office  Online，但是这个软件只有联网的时候才能用，而且office  online在国内的服务貌似有点问题，长期报错。我还是比较推荐大家使用金山出品的WPS，而且现在已经更新到WPS2019了，UI和以往完全不同，看起来非常舒服。执行下面2步即可安装WPS：

```bash
sudo pacman -S wps-office
sudo pacman -S ttf-wps-fonts
或者
yay -S wps-office
yay -S ttf-wps-fonts
```

#### （6）、Typora

```bash
yay -S typora
```

#### （7）、Chrom

```bash
yay -S chromium
```

#### （8）Git ssh

```bash
yay -S git openssh
```



#### （9）、Oh-my-zsh

1. 查看本地有哪几种shell `cat /etc/shells`

2. 切换到zsh `chsh -s /bin/zsh`

3. 安装 oh-my-zsh的配置文件

   ```bash
   via wget
   sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
   **或者**
   via curl
   sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
   ```

   

4. 简单配置oh-my-zsh

   （1）、主题

    终端输入：

```bash
sudo vim ~/.zshrc
```

​        找到 `ZSH_THEME="robbyrussell"`

​        修改为 `ZSH_THEME="ys"` ，修改为ys主题，要换其他主题，修改此处即可

​        修改完后重新打开zsh即可

​      （2）、插件

```bash
# z命令快速跳转目录     x命令解压一切文件         命令行可以直接google  
plugins=(
  git z zsh-autosuggestions extract web-search zsh-syntax-highlighting 
)
#自动补全插件
source ~/.oh-my-zsh/plugins/incr/incr.zsh
 
# 自动更新的时间间隔，单位是天，这里设置 30 天更新一次
export UPDATE_ZSH_DAYS=30
```



#### （10）、中文汉化

```bash
sudo pacman -S --noconfirm firefox-i18n-zh-cn thunderbird-i18n-zh-cn gimp-help-zh_cn libreoffice-still-zh-CN man-pages-zh_cn
```

**Firefox汉化**

1. 输入 *about:config*

2. 搜索 *intl.locale.requested*

3. 值 *zh_CN*

   
