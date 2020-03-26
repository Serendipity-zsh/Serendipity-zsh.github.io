---
layout: post
title: "Linux的shell脚本"
date: 2019-07-10
tag: Linux,shell
---



# Linux的shell脚本

## 基本规范

+ 凡是自定义的脚本建议放到/usr/local/sbin/目录下，这样做的目的是，一来可以更好的管理文档；二来以后接管你的管理员都知道自定义脚本放在哪里，方便维护

+ Shell脚本通常都是以.sh 为后缀名的

+ 第一行一定是 “#! /bin/bash” 它代表的意思是，该文件使用的是bash语法。如果不设置该行，那么你的shell脚本就不能被执行。’#’表示注释，在前面讲过的。后面跟一些该脚本的相关注释内容以及作者和创建日期或者版本等等

+ Shell脚本的执行很简单，直接”sh filename “ 即可

+ 使用sh命令去执行一个shell脚本的时候是可以加-x选项来查看这个脚本执行过程的，这样有利于我们调试这个脚本哪里出了问题

+ chmod +x test.sh  ./test.sh

  ```shell
  #shell脚本中用到了’date’这个命令，它的作用就是用来打印当前系统的时间
  date "+%Y/%m/%d %H:%M%S"
  #-d 选项也是经常要用到的，它可以打印n天前或者n天后的日期，当然也可以打印n个月/年前或者后的日期
  date -d "-1 day" "+%Y/%m/%d"
  #星期几
  date +%w
  ```

  

## 基本语法

### 定义变量

格式为 “变量名=变量的值”。当在脚本中引用变量时需要加上’$’符号

```shell
#! /bin/bash
a=`date +%H:%M:%S`
echo "this is time:$a"
# 记得反引号
```



### 数学计算

要用’[ ]’括起来并且外头要带一个’$’

```shell
#! /bin/bash
a=1
b=2
sum=$[$a+$b]
```

### 用户交互

```shell
# read命令 可以从标准输入获得变量的值，后跟变量名
#! /bin/bash
echo "请输入一个数字"
read x
echo "请输入另一个数字"
read y
sum=$[$x+$y]
```

```shell
#read -p 选项类似echo的作用
#! /bin/bash
read -p "请输入一个数字：" x
read -p "请输入另一个数字：" y
sum=$[$x+$y]
```

### 预设变量

```shell
# shell脚本在执行的时候后边是可以跟变量的，而且还可以跟多个
# 前面的/etc/init.d/iptables 文件其实就是一个shell脚本 
# /etc/init.d/iptables restart

#! /bin/bash
sum=$[$1+$2]
echo $sum

sh -x test.sh 1 2

# $1和$2，这其实就是shell脚本的预设变量，其中$1的值就是在执行的时候输入的1，而$2的值就是执行的时候输入的$2，当然一个shell脚本的预设变量是没有限制的
# 另外还有一个$0，不过它代表的是脚本本身的名字
```

### 逻辑判断

+ 不带 else

```shell
#! /bin/bash
read -p "请输入你的分数：" x
if ((x>60)); then
	echo "你的成绩合格。"
fi
# ((a<60))这样的形式，这是shell脚本中特有的格式，用一个小括号或者不用都会报错
```

+ 带有 else

```shell
#! /bin/bash
read -p "请输入你的分数：" x
if ((x>60)); then
	echo "你的成绩合格。"
else
	echo "你的成绩不合格。"
fi
```

+ 带有elif

```shell
#! /bin/bash
read -p "请输入你的分数：" x
if ((x<60)); then
	echo "你的成绩不合格。"
elif ((x>60))&&((x<85)); then
	echo "你的成绩不错"
else
	echo "你的成绩非常好"
fi

#  -lt （小于），-gt （大于），-le （小于等于），-ge （大于等于），-eq （等于），-ne （不等于）
```

+ shell 脚本中if还经常判断关于 **档案属性**，比如判断是 **普通文件** 还是 **目录**，判断文件是否有 **读写执行权限 **

> -e ：判断文件或目录是否存在
>
> -d ：判断是不是目录，并是否存在
>
> -f ：判断是否是普通文件，并存在
>
> -r ：判断文档是否有读权限
>
> -w ：判断是否有写权限
>
> -x ：判断是否可执行
>
> 具体格式为： if [ -e filename ] ; then

```shell
if [ -d /home ]; then echo ok; fi
```

+ 在shell 脚本中，除了用if来判断逻辑外，还有一种常用的方式，那就是case

```shell
#! /bin/bash
read -p "请输入一个数字：" n
a=$[$n%2]
case $a in
1)
	echo "这是个奇数"
	;;
0)
	echo "这是个偶数"
	;;
esac

# case脚本常用于编写系统服务的启动脚本，例如/etc/init.d/iptables中就用到了
```

### 循环

+ for 循环

```shell
#! /bin/bash
for i in `seq 1 5`; do
	echo $i
done
# seq 1 5 表示从1到5的一个序列

for i in `ls`; do echo $i; done 

for i in `cat test.txt`； do echo $i; done
```

+ while 循环

```shell
#! /bin/bash
a=10
while [$a -ge 1 ]; do
	echo "$a"
	a=$[$a-1]
done

# 可以把循环条件忽略掉，笔者常常这样写监控脚本。
while :; do
command
done
```

### 函数

```shell
#! /bin/bash
function sum(){
	sum=$[$1+$2]
	echo $sum
}
sum $1 $2
# 在shell脚本中，函数一定要写在最前面，不能出现在中间或者最后，因为函数是要被调用的，如果还没有出现就被调用，肯定是会出错的
```

