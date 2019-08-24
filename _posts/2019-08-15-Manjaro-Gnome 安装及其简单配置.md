---
layout: post
title: "Manjaro-Gnome安装及其简单配置"
date: 2019-08-15 
description: "Linux Arch，Manjaro Gnome，Gnome简单配置"
tag: Manjaro 
---   
## 一、安装

1、官网下载iso镜像，可以选择三个主题GNOME、KDE和

我用的是GNOME

2、用rufus刻录

刻录时除了修改一下刻录方式为：**DD模式**，其他默认就可以（刻录方式一定要改，不然gg）

3、U盘启动，进入Manjaro安装引导

分区前面的步骤，一看就知道了，这里说一下分区：顶端选择固态硬盘，然后选择**手动分区**，

下一步，然后在你要给Manjaro的分区里面分个100Mb的分区: FAT32、/boot/efi、ecp

其余的ext4、/   ，然后下一步就可以了



## 二、配置



1、更换国内源

使用国内的源有更快的下载速度，pacman能够测试不同源的速度并给它们排名，从中选择一个快的即可。

```bash
sudo pacman -Syu
sudo pacman-mirrors -i -c China -m rank
sudo pacman -Syu
```

2、添加arch源

编辑/etc/pacman.conf文件

(刚开始没有vim，先在终端输入:

```bash
sudo pacman -S vim
```

安装vim），然后在输入：

```bash
sudo vim /etc/pacman.conf
```

添加下面内容：

```bash
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.sjtug.sjtu.edu.cn/archlinux-cn/$arch
```

**也可以在添加软件工具中，在首选项中打开AUR，从AUR检查更新即可**

然后

```bash
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```

现在可以升级一下系统：

sudo pacman -Su

3、安装aria2并加速源下载

```bash
sudo pacman -S aria2
```


```bash
vim /etc/pacman-mirrors.conf
```

修改如下地方为中国：

```bash
OnlyCountry = China
```

4、更新源列表

```bash
sudo pacman -Syyu
```

5、安装archlinuxcn-keyring

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



6、导入GPG Key

```bash
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```

此后就可以去安装自己想要使用软件啦！！

7、刷新缓存

```bash
sudo pacman -Syy
```

这一步是为了更新缓存以及导入密钥链。

**解决双系统时差相差8小时**

```bash
sudo timedatectl set-local-rtc true
```

然后再重启manjaro系统就可以了

## 三、美化

可以找自己喜欢的主题，然后下载

https://linux.cn/article-10720-1.html

在谷歌浏览器中装gnome-shell-extensions，可以在你的manjaro里添加各种扩展

## 四、安装常用的工具

1、安装Yay

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

2、输入法

安装fcitx

```bash
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im
sudo pacman -S fcitx-configtool
```

fcitx为基础安装包，fcitx-im 用于GTK/QT等界面上使用的包，fcitx-configtool为配置界面（kde下还能装一个kde版的configtool）

设置环境变量，在~/.xprofile文件（如果文件不存在就新建一个）末尾加上：

```bash
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

中文字体

安装如下字体：

```bash
sudo pacman -S ttf-roboto noto-fonts ttf-dejavu
```

文泉驿

```bash
sudo pacman -S wqy-bitmapfont wqy-microhei wqy-microhei-lite wqy-zenhei
```

思源字体

```bash
sudo pacman -S noto-fonts-cjk adobe-source-han-sans-cn-fonts adobe-source-han-serif-cn-fonts
```

安装搜狗输入法

```bash
yay -S fcitx-sogoupinyin
```

2、QQ & Tim

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

3、WPS

虽然Manjaro自带Microsoft Office  Online，但是这个软件只有联网的时候才能用，而且office  online在国内的服务貌似有点问题，长期报错。我还是比较推荐大家使用金山出品的WPS，而且现在已经更新到WPS2019了，UI和以往完全不同，看起来非常舒服。执行下面2步即可安装WPS：

```bash
sudo pacman -S wps-office
sudo pacman -S ttf-wps-fonts
或者
yay -S wps-office
yay -S ttf-wps-fonts
```

4、Typora

```bash
yay -S typora
```

5、Chrom

```bash
yay -S chromium
```

6、Git ssh

```bash
yay -S git openssh
```

7、Oh-my-zsh

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
# z命令快速跳转目录，这个需要有路径记录
#x命令解压一切文件         
#命令行可以直接google  
plugins=(
  git z zsh-autosuggestions extract web-search zsh-syntax-highlighting 
)
#自动补全插件
source ~/.oh-my-zsh/plugins/incr/incr.zsh
 
# 自动更新的时间间隔，单位是天，这里设置 30 天更新一次
export UPDATE_ZSH_DAYS=30
```

8、中文汉化

```bash
sudo pacman -S --noconfirm firefox-i18n-zh-cn thunderbird-i18n-zh-cn gimp-help-zh_cn libreoffice-still-zh-CN man-pages-zh_cn
```

9、安装Mysql

```bash
#下载mysql
yay -S mysql
# 初始化
$ sudo mysqld --initialize --user=mysql --basedir=/usr --datadir=/var/lib/mysql
#记住初始的密码，在初始化之后，一般都在输出的文字的最后，就是在localhost：后面的一串字符
#也可以修改配置文件免密登录，修改/etc/mysql/my.cnf
sudo vim /etc/mysql/my.cnf
在最后一行加上
skip-grant-tables
#设置开机自启mysql
sudo systemctl enable mysqld.service
#启动mysql
sudo systemctl start mysqld
mysql -u root -p
#如果前面设置了免密，就不用输密码，直接回车进入
#修改密码
ALTER user 'root'@'localhost' IDENTIFIED BY 'new_password';

```

上面的情况是一切顺利的前提，但是基本不太可能，如果你是上面的情况，那你。。。NB

问题一：

```bash
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

问题二：

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

