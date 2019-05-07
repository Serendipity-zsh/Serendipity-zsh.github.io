---
layout: post
---
<img src="/images/fulls/03.jpg" class="fit image"> 
# Leaving-Wechat 项目记录

## 1、微信小程序测试号

AppID ：wxcab27a84d5444811

AppSecret ：b4093a612ecde19fc05456763ee95c06

## 2、内网穿透：ngrok

暴露出本地端口号，自动生成一个域名，可远程访问，记录访问情况

<img src="/images/leavings/ngrok01.jpg" class="fit image"> 

<img src="/images/leavings/ngrok02.jpg" class="fit image"> 


Session Status           online

Account                  Serendipity (Plan: Free)

Version                  2.3.25

Region                   United States (us)

Web Interface            http://127.0.0.1:4040

Forwarding               http://073174a5.ngrok.io -> http://localhost:8080

Forwarding               https://073174a5.ngrok.io -> http://localhost:8080

Connections              ttl     opn     rt1     rt5     p50     p90

                         0       0       0.00    0.00    0.00    0.00


## 3、小程序登陆授权

参考：https://www.imooc.com/video/18061


前端授权成功，获取临时凭证code，传给后台；


后台得到后，根据code，传自己的appid，secret给微信api，获得微信用户的openId和session_key


## 4、mybatis链接数据库

参考：https://www.imooc.com/learn/945


## 5、redis配置

整合参考：https://www.cnblogs.com/zeng1994/p/03303c805731afc9aa9c60dbbd32a323.html


[菜鸟教程]("https://www.runoob.com/redis/redis-tutorial.html")


## 6、RESTful API设计：

<img src="/images/leavings/APIdesign.jpg" class="fit image"> 


## 7、错误集锦


### 1、service层

#### 错误描述：
2019-04-14 22:09:52.027 ERROR 8416 --- [nio-8082-exec-5] o.a.c.c.C.[.[.[/].[dispatcherServlet]    : Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Request processing failed; nested exception is org.apache.ibatis.binding.BindingException: Invalid bound statement (not found): com.wechat.springbootleaving.dao.AreaDao.queryArea] with root cause

#### 解决：

service层必须和serviceImpl层放在同一个包下

### 2、数据库连接池

#### 描述： 

{"success":false,"errMsg":"Handler dispatch failed; nested exception is java.lang.AbstractMethodError: Method com/mchange/v2/c3p0/impl/NewProxyResultSet.isClosed()Z is abstract"}

#### 解决：

<img src="/images/leavings/lianjiechi.jpg" class="fit image"> 

### 3、扫描不到controller

#### 描述：

打开localhost：一直报404，连hello都输不出来

#### 解决：

启动类中：MapperScan("dao层路径")

### 4、数据库时区

#### 描述：

The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone

#### 解决：
<img src="/images/leavings/shiqu.jpg" class="fit image"> 

### 5、数据库调不出来

#### 描述：

简单hello可以输出，调用数据库出错

{“success”：false，“errMsg”：“嵌套异常是org.apache.ibatis.exceptions.PersistenceException：\ r \ n ###错误查询数据库。原因：org.springframework.jdbc.CannotGetJdbcConnectionException：无法获取JDBC连接;嵌套异常是java.sql.SQLException：无法从底层数据库获取连接！\ r \ n ###文件中可能存在错误[D：\微信小程序参考\微信小程序前后端源码\ zhang-demo-test \ target \ classes \ mapper \ AreaDao.xml] \ r \ n ###错误可能涉及com.imooc.demo1.zhangdemotest.dao.AreaDao.queryArea \ r \ n ###执行查询时发生错误\ r \ n ###原因：org.springframework.jdbc.CannotGetJdbcConnectionException：无法获取JDBC连接;嵌套异常是java.sql.SQLException：无法从中获取连接底层数据库！“}

#### 解决：

实体里面多了asex，aname,adepartment的get和set方法

### 6、数据库密码错误

#### 描述：

Access denied for user 'root'@'localhost' (using password: YES) 

#### 解决：

改正mybatis或者jdbc配置中mysql数据库密码，检查数据库是否断开连接

### 7、web界面显示错误

#### 描述：
<img src="/images/leavings/webWrong.jpg" class="fit image"> 

#### 解决：

Admin实体里面多了asex，aname,adepartment的get和set方法

### 8、数据显示错误

#### 描述：
<img src="/images/leavings/dataDisplay.jpg" class="fit image"> 

#### 解决：

实体的属性名与数据库中属性名不一致
