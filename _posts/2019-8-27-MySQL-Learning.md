---
layout: post
title: MySQL-learning
date: 2019-8-27
description: MySQL学习记录，速查表
tag: "MySQL"
---



# MySQL学习笔记

## 1、DBMS

两类：

### 1.1 基于共享文件系统的DBMS

例如：Microsoft Access和FileMaker

用于桌面用途，通常不用于高端或更关键的应用。

### 1.2 基于客户机-服务器的DBMS

例如：MySQL、Oracle、Microsoft SQL Server

服务器：负责所有数据访问和处理的一个软件，这个软件运行在称为**数据库服务器**的计算机上。与数据文件打交道的只有服务器软件，关于数据、数据添加、删除和数据更新的所有请求都由服务器软件完成。

客户机：与用户打交道的软件，客户机通过网络提交请求给软件服务器，服务器软件处理请求，把结果送到客户机软件。

## 2、mysql命令

开始：mysql>之后

结束：用;或\g，仅enter不执行命令

帮助：help或\h，也可以指定提示，例如：help select

退出：quit或者exit

## 3、MySQL Administrator

图形交互客户机，简化MySQL服务器管理

提示输入服务器和登录信息（允许保存服务器定义供以后使用），然后显示允许选择不同视图的图标：

Server Information（服务器信息）显示客户机和被连接的服务器的状态和版本信息；

Service Control（服务控制）允许启动和停止MySQL以及指定服务器特性；

User Administration（用户管理）用来定义MySQL用户、登录和权限；

Catalogs（目录）列出可用的数据库并允许创建数据库和表。

## 4、使用MySQL

### 4.1 选择数据库

```mysql
#选择数据库test
USE test;
```

### 4.2 显示数据

```mysql
#显示所有的数据库
SHOW DATABASE;

#显示test数据库的tables
USE test;
SHOW TABLES;

#显示test数据库中table1表的columns
USE test;
SHOW COLUMNS FROM table1;

#或者直接用下面的语句操作显示columns
USE test;
DESCRIBE table1;

#显示服务器状态信息
SHOW STATUS;

#显示创建特定数据库
SHOW CREATE DATABASE;

#显示创建特定表
SHOW CREATE TABLE;

#显示授权用户
SHOW GRANTS;

#显示服务器错误或警告信息
SHOW ERRORS;
SHOW WARNINGS;

#查看所有的show语句
HELP SHOW;
```

### 4.3 检索数据

```mysql
#注意多条sql语句是以;结尾的，且sql语句不区分大小写
#可以考虑所有SQL关键字用大写，而对所有列和表名使用小写
USE test;
#检索test数据库table1表的column1列
SELECT column1
FROM table1;

#检索多个列
SELECT column1,column2,column3
FROM table1;

#检索所有列
SELECT *
FROM table1;

#检索不同的行
#注意不能部分使用DISTINCT，例如两个column，两个中的任何一个不同都会显示出来
SELECT DISTINCT column1
FROM table1;

#注意检索出来的第1行为行0
#限制结果，显示前5行
SELECT column1
FROM table1
LIMIT 5;

#指定检索开始的行和行数，从行5开始的5行
SELECT column1
FROM table1
LIMIT 5,5;
#或者下面
#从行5开始取5行
LIMIT 5 OFFSET 5

#使用完全限定的表明和列名
SELECT table1.column1
FROM test.table1;

```



### 4.4 排序检索数据

```mysql
#ORDER BY子句位置： 位于FROM子句后，LIMIT子句前,如果有WHERE子句，位于WHERE之后
USE test;
#按照column1列排序
#按照多个列排序，逗号隔开就可以，但有排序优先性
#默认升序(ASC)排列
SELECT *
FROM table1
ORDER BY column1;

#指定降序排列
SELECT *
FROM table1
ORDER BY column1 DESC;

#再多个列上降序排列，需要对每个列指定DESC
#混合排序，column1降序排列，再对column2排序
SELECT *
FROM table1
ORDER BY column1 DESC,column2;

```



### 4.5 过滤数据

```mysql
#MySQL执行匹配时不区分大小写
#条件过滤检索
USE test;
#显示column1=1的数据
SELECT *
FROM table1
WHERE column1=1;

#WHERE子句操作符
# =、<>、!=、<、<=、>、>=、BETWEEN AND

#空值检查
WHERE column1 IS NULL;

```

### 4.6 数据过滤

```mysql
#优先处理AND操作符，复杂操作使用括号明确分组
USE test;
#AND操作符，同时满足多个条件
SELECT *
FROM table1
WHERE column1=1 AND column2<=2;

#OR操作符，满足其中的一种条件
SELECT *
FROM table1
WHERE column1=1 OR column2<=2;

#IN操作符
#适用情况：在使用长的合法选项清单时;计算次序容易管理;执行速度块;可以包含其他SELECT语句
SELECT *
FROM table1
WHERE column1 IN(1,2);

#NOT操作符
#匹配column1不等于1和2的
SELECT *
FROM table1
WHERE column1 NOT IN(1,2);

```



### 4.7 通配符过滤

```mysql
#LIKE操作符
USE test
#百分号（%）通配符，区分大小写,可以匹配0个字符，但是不能匹配NULL
#检索任意以he起头的词
#搜索模式'he%'
SELECT *
FROM table1
WHERE column1 LIKE 'he%';
#多个通配符，任意位置包含hello的
SELECT *
FROM table1
WHERE column1 LIKE '%hello%';

#下划线（_）通配符
#只匹配单个字符(不能多也不能少)
SELECT *
FROM table1
WHERE column1 LIKE '_ hello';
#输出：
column1      column2
   1         a hello
   2         b hello
   
#通配符技巧
#所花费时间长
#优先选择其他操作达到目的
#尽量不要放在搜索开始处
```

### 4.8 正则表达式进行搜索

```mysql
#用途：
#提取电话号码;查找名字中有数字的;
#文本块中找到所有重复单词;替换页面中所有URL为这些URL的上实际HTML链接

#不区分大小写，想要区分使用BINARY关键字：WHERE column1 REGEXP BINARY 'hello .00'

USE test;
#检索column1包含文本100的所有行
SELECT *
FROM table1
WHERE column1 REGEXP '100'
ORDER BY column1;

#.是正则表达式语言中一个特殊的字符，表达匹配任意一个字符
SELECT column1
FROM table1
WHERE column1 REGEXP '.00';
输出：
column1
a hello 100
b hello 200
#LIKE '100'与REGEXP '100'的区别：
LIKE匹配整个列，对应列值完全相等才会匹配,REGEXP则是包含就匹配

#进行OR匹配
SELECT column1
FROM table1
WHERE column1 REGEXP '100|200'
ORDER BY column1 DESC;
输出：
column1
hello 200
hello 100

#匹配几个字符之一
#正则表达式[123]表示：匹配1或2或3
#[]是另一种形式的OR语言
SELECT column1
FROM table1
WHERE column1 REGEXP '[123] hello'
ORDER BY column1 DESC;
输出：
column1
2 hello z
1 hello s

#使用'1|2|3 hello'
#这里的意思是：'1'或'2'或'3 hello'
SELECT column1
FROM table1
WHERE column1 REGEXP '1|2|3 hello'
ORDER BY column1;
输出：
1 hello z
2 hello s
world 100
world 200

#否定字符集匹配
SELECT column1
FROM table1
WHERE column1 REGEXP '[^123] hello'
ORDER BY column1 DESC;

#匹配范围
#[1-3]、[6-9]、[a-z]
SELECT column1
FROM table1
WHERE column1 REGEXP '[1-5] hello'
ORDER BY column1;
输出：
.5 hello a
1 hello z
2 hello s

#匹配特殊字符
#因为.匹配任意字符，因此每个行都被检索
#匹配特殊字符必须用\\为前导，\\-表示查找-，\\.表示查找.。
#正则表达式中具有特殊意义的所有字符都必须用\\转义字符，包括.、|、[]
#\\也用来引用元字符：
\\f    换页
\\n    换行
\\r    回车
\\t    制表
\\v    纵向制表

SELECT column1
FROM table1
WHERE column1 REGEXP '\\.'
ORDER BY column1;
```



**匹配字符类**

|     类     |                            说明                            |
| :--------: | :--------------------------------------------------------: |
| [:alnum:]  |              任意字母和数字（同[a-zA-Z0-9]）               |
| [:alpha:]  |                   任意字符（同[a-zA-z]）                   |
| [:blank:]  |                   空格和制表（同[\\\t]）                   |
| [:cntrl:]  |             ASCII控制字符（ASCII 0到31和127）              |
| [:digit:]  |                    任意数字（同[0-9]）                     |
| [:graph:]  |               与[:print:]相同，但不包括空格                |
| [:lower:]  |                  任意小写字母（同[a-z]）                   |
| [:print:]  |                       任意可打印字符                       |
| [:punct:]  |         既不在[:alnum:]又不在[:cntrl:]中的任意字符         |
| [:space:]  | 包括空格在内的任意空白字符（同[\\\f \\\n \\\r \\\t \\\v]） |
| [:upper:]  |                  任意大写字母（同[A-Z]）                   |
| [:xdigit:] |             任意十六进制数字（同[a-fA-F0-9]）              |



**匹配多个实例**

​                                                                            **重复元字符**

| 元字符 |             说明             |
| :----: | :--------------------------: |
|   *    |        0个或多个匹配         |
|   +    |        1个或多个匹配         |
|   ?    |         0个或1个匹配         |
|  {n}   |        指定数目的匹配        |
|  {n,}  |     不少于指定数目的匹配     |
| {n,m}  | 匹配数目的范围（m不超过255） |

```mysql
#例子
USE test;
#匹配（、0-9、sticks、stick、）
SELECT column1
FROM table1
WHERE column1 REGEXP '\\([0-9] sticks?\\)'
ORDER BY column1;
输出：
column1
TNT （1 stick）
TNT （5 sticks）

#匹配连在一起的4位数字
SELECT column1
FROM column1
WHERE column1 REGEXP '[[:digit:]]{4}'
ORDER BY column1;
#或者
SELECT column1
FROM column1
WHERE column1 REGEXP '[0-9][0-9][0-9][0-9]'
ORDER BY column1;
输出：
column1
hello 1000
hello 2000
```



**定位符**

| 元字符  |                说明                 |
| :-----: | :---------------------------------: |
|    ^    | 文本的开始/在集合中用其来否定该集合 |
|    $    |             文本的结尾              |
| [[:<:]] |              词的开始               |
| [[:>:]] |              词的结尾               |

```mysql
#以一个数开始的所有产品（或者以小数点开始的数）
#[0-9\\.]或[[:digit:]\\.]均不能实现
SELECT column1
FROM table1
WHERE column1 REGEXP '^[0-9\\.]'
ORDER BY column1;
#^[0-9\\.]只在.或任意数字为串中第一个字符时才匹配它们

#使REGEXP起类似LIKE的作用
用^开始每个表达式，用$结束每个表达式
```

**简单的正则表达式测试**

```mysql
#REGEXP检查总是返回0（没有匹配）或1（匹配），可以用带文字串的REGEXP来测试表达式
SELECT 'hello' REGEXP '[0-9]';
```



### 4.9 创建计算字段

**MySQL的不同之处：**多数DBMS使用+或||来实现拼接，MySQL则使用Concat()函数来实现，当把SQL语句转换成MySQL语句时一定要记得这个区别。

```mysql
USE test;
#检索后输出：column1(column2)这样的格式
SELECT Concat(column1,'(',column2,')')
FROM table1
ORDER BY column1;
#Concat()需要一个或多个指定的串，各个串之间用逗号分隔

#通过删除数据右侧多余的空格来整理数据
#RTrim()函数去掉右边的所有空格
SELECT Concat(RTrim(column1),'(',RTrim(column2),')')
FROM table1
ORDER BY column1;
#LTrim()函数去掉左边的空格，Trim()函数去掉两边的空格

#使用别名（alias），一个字段或值的替换名
#用AS关键字赋予
SELECT Concat(RTrim(column1),'(',RTrim(column2),')') AS column_title
FROM table1
ORDER BY coulmn1;
#别名用途：重命名包含不符合规定字符的实际表列名;在原来的名字含混或容易误解时扩充它;有时称为导出列

#执行算术计算
SELECT column1,column2,column1*column2 AS column_sum
FROM table1;
MySQL算术操作符：
操作符    说明
  +       加
  -       减
  *       乘
  /       除
  
#测试计算
SELECT 3*2;
#测试Trim()函数
SELECT Trim(' abc ');
#测试Now()函数,返回当前日期和时间
SELECT Now();
```



### 4.10 使用数据处理函数

大多数SQL实现支持的函数类型：

用于处理文本串（如删除或填充值，转换值为大写或小写）的文本函数；

用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数；

用于处理日期和时间值并从这些值中提取特定成分（例如，返回两个日期之差，检查日期有效性等）的日期和时间函数；

返回DBMS正使用的特殊信息（如返回用户登录信息，检查版本细节）的系统函数

#### 文本处理函数

```mysql
USE test;

#Upper()将文本转换为大写
SELECT column1,Upper(column1) AS column1_upcase
FROM table1
ORDER BY column1;
输出：
column1   column1_upcase
  abc	       ABC
  def          DEF

```

常用的文本处理函数

|    函数     |       说明        |
| :---------: | :---------------: |
|   Left()    | 返回串左边的字符  |
|  Length()   |   返回串的长度    |
|  Locate()   | 找出串的一个子串  |
|   Lower()   |  将串转换为小写   |
|   LTrim()   | 去掉串左边的空格  |
|   Right()   | 返回串右边的字符  |
|   RTrim()   | 去掉串右边的空格  |
|  Soundex()  | 返回串的SOUNDEX值 |
| SubString() |  返回字串的字符   |
|   Upper()   |  将串转换为大写   |

SOUNDEX：将文本串转换为发音描述，可以查找出发音类似的不同值

```mysql
#Sundex()函数
SELECT column1,column2
FROM table1
WHERE Soundex(column1)=Soundex('Y Lie');
输出：
column1   column2
  Inc      Y Lee
```



#### 日期和时间处理函数

常用日期和时间处理函数

|     函数      |              说明              |
| :-----------: | :----------------------------: |
|   AddDate()   |    增加一个日期（天、周等）    |
|   AddTime()   |    增加一个时间（时、分等）    |
|   CurDate()   |          返回当前日期          |
|   CurTime()   |          返回当前时间          |
|    Date()     |     返回日期时间的日期部分     |
|  DateDiff()   |        计算两个日期之差        |
|  Date_Add()   |       高度灵活的日期函数       |
| Date_Format() |  返回一个格式化的日期或时间串  |
|     Day()     |     返回一个日期的天数部分     |
|  DayOfWeek()  | 对于一个日期，返回对应的星期几 |
|    Hour()     |     返回一个时间的小时部分     |
|   Minute()    |     返回一个时间的分钟部分     |
|    Month()    |     返回一个日期的月份部分     |
|     Now()     |       返回当前日期和时间       |
|   Second()    |      返回一个时间的秒部分      |
|    Time()     |   返回一个日期时间的时间部分   |
|    Year()     |     返回一个日期的年份部分     |

```mysql
#使用Date()函数
SELECT column1,column2
FROM table1
WHERE Date(coulmn_date)='2019-09-02';
#Date()函数返回日期时间的日期
#虽然可能column_date为日期，但最好还是用Date()函数
#Time也同样类似
#日期格式：yyyy-mm-dd
#时间格式：00:00:00

#检索2019年9月下的数据
SELECT column1,column2
FROM table1
WHERE Year(coulmn_date)='2019' AND Month(column_date)='9'BETWEEN '2019-09-01' AND '2019-09-30';
#或者下面不用考虑每个月多少天
SELECT column1,column2
FROM table1
WHERE Year(coulmn_date)=2019 AND Month(column_date)=9;
```

#### 数值处理函数

|  函数  |        说明        |
| :----: | :----------------: |
| Abs()  | 返回一个数的绝对值 |
| Cos()  | 返回一个角度的余弦 |
| Exp()  | 返回一个数的指数值 |
| Mod()  |  返回除操作的余数  |
|  Pi()  |     返回圆周率     |
| Rand() |   返回一个随机数   |
| Sin()  | 返回一个角度的正弦 |
| Sqrt() | 返回一个数的平方根 |
| Tan()  | 返回一个角度的正切 |



### 4.11 汇总数据



## 5、MySQL性能

存储引擎：XtraDB、XtraBackup