---
layout: port
title: " Organization项目学习记录"
date: 2019-11-15
description: "Spring,Java"
tag: Spring
---



# Organization项目学习记录

## 1. 加盐加密

### 加盐

设置一个特定的字符串（静态变量）

例如：

```java
public static final String ENCRYPT_SALT = "FeidianStudio:Copyright2019.organization"
```

### 加密

使用加密算法将给定的字符串密码加密（**一个字符串对应一个加密后的字符串**）

#### 常用加密工具和算法

#### Spring 的 DigestUtils

```java
//只有MD5加密
//不可逆算法
String string = new String("zhangshihao");
String firstlyEncode = DigestUtils.md5DigestAsHex(string.getBytes());
```

#### Commons-codec

需要提供commons-codec包

```java
//MD5:不可逆算法
String firstlyEncode = DigestUtils.md5Hex(string);

//SHA1:不可逆算法
String firstlyEncode = DigestUtils.shaHex(string);

//BASE64:可逆算法
//加密
byte[] b = Base64.encodeBase64(string.getBytes(),true);
String firstlyEncode = new String(b);

//解密
byte[] b1 = Base64.decodeBase64(firstlyEncode); 
```

#### 加盐加密结果

```java
//firstlyEncode为原始信息加密后的结果
String secondEncode = firstlyEncode + UtilConstant.ENCRYPT_SALT;
//最终结果为：对（原始信息加密+盐）再次加密
String finallyEncode = DigestUtils.md5DigestAsHex(secondlyEncode.getBytes());

```



## 2. Redis

#### StringRedisTemplate 常用操作

```java
StringRedisTemplate stringRedisTemplate;

stringRedisTemplate.opsForValue().set("test", "100",60*10,TimeUnit.SECONDS);//向redis里存入数据和设置缓存时间
stringRedisTemplate.boundValueOps("test").increment(-1);//val做-1操作
stringRedisTemplate.opsForValue().get(key)//根据key获取缓存中的val
stringRedisTemplate.boundValueOps("test").increment(1);//val +1
stringRedisTemplate.getExpire("test")//根据key获取过期时间
stringRedisTemplate.getExpire("test",TimeUnit.SECONDS)//根据key获取过期时间并换算成指定单位
stringRedisTemplate.delete(key);//根据key删除缓存
stringRedisTemplate.hasKey("546545");//检查key是否存在，返回boolean值
stringRedisTemplate.opsForSet().add("red_123", "1","2","3");//向指定key中存放set集合
stringRedisTemplate.expire("red_123",1000 , TimeUnit.MILLISECONDS);//设置过期时间
stringRedisTemplate.opsForSet().isMember("red_123", "1")//根据key查看集合中是否存在指定数据
stringRedisTemplate.opsForSet().members("red_123");//根据key获取set集合
```



## 3. 拦截器

```java
各种拦截器都实现HandlerInterceptor这个接口
```

设置Header

```java
//先获取http传过来的url信息（其中可能包含我们设置的Header）
String url = httpServletRequest.getRequestURI();
//Constant.AUTHORIZATION的值为Authorization
//获取url中包含我们设置的Header的数据，用来判断目前访问后台的用户的身份
String authentication = httpServletRequest.getHeader(Constant.AUTHORIZATION);
```

对前端传过来的请求（HttpServletRequest）的信息进行解析

```java

//接下来先判断http传过来的url是否需要拦截（因为可能url是我们用swagger来调接口的）
调用DirectURLUtil的isDirectURL(url)方法
public class DirectURLUtil {
    public static boolean isDirectURL(String url) {
        log.info("url:{}", url);
        for (String item : UtilConstant.urls) {
            Pattern pattern = Pattern.compile(item);
            Matcher matcher = pattern.matcher(url);
            if (matcher.matches()) {
                return true;
            }
        }
        return false;
    }
    public static final String[] urls = {
            "/",
            // swagger
            "/swagger-ui.html",
            "/error",
            "/csrf",
            "/webjars/springfox-swagger-ui/([a-zA-Z0-9\\/_\\.-]+)",
            "/swagger-resources(/([a-zA-Z0-9\\/_\\.-]+))?",
            "/account/login/mail/[\\d]",
    };
}
```

根据对HttpServletRequest的解析做出”反应“（HttpServletResponse），返回给前端

```java
httpServletResponse.setCharacterEncoding("utf-8");
httpServletResponse.setContentType("application/json;charset=utf-8");

//判断前面我们获得的身份信息 authentication
String authentication = httpServletRequest.getHeader(Constant.AUTHORIZATION);
//未登录
if (authentication == null) {
    response = Responses.errorResponse("未登录，请登录后再试");
    response.setMeta(response.getMeta().setCode(401));
    log.error("未登录，请登录");
    httpServletResponse.setStatus(401);
    try {
        PrintWriter writer = httpServletResponse.getWriter();
        //把response转换成json串打印在日志上
        log.info(objectMapper.writeValueAsString(response));
        //把response转换为json串后写入httpServletResponse
        writer.write(objectMapper.writeValueAsString(response));
    } catch (IOException e) {
        log.error(e.getClass() + "\n" + e.getCause());
    }
    return false;
}
//已登陆
try {
    //id为':'之前的内容
    String id = authentication.substring(0, authentication.indexOf(':'));
    log.info("id = {}, index of ':' is {}.", id, authentication.indexOf(':'));
    //token为':'之后的内容
    String token = authentication.substring(authentication.indexOf(':') + 1);
    log.info("token is {}.", token);
    //传入get()方法，把id加盐加密一次之后，当作key传入stringRedisTemplate.opsForValue().get(key)方法，获取redis中对应key的value值
    String redisToken = redisUtil.get(id);
    log.info("in redis the token is {}.", redisToken);
    //判断前端传过来的HttpServletRequest中的token（身份信息）是否与redis中的一致
    if (token.equals(redisToken)) {
        log.info("已登录，退出帐号拦截器");
        return true;
    } else {
        PrintWriter writer = httpServletResponse.getWriter();
        response = Responses.errorResponse("token错误");
        response.setMeta(response.getMeta().setCode(400));
        //把错误信息写入HttpServletResponse，传回给前端
        writer.write(objectMapper.writeValueAsString(response));
        httpServletResponse.setStatus(400);
        log.error("token错误");
        return false;
    }
} catch (IOException e) {
    httpServletResponse.setStatus(400);
    e.printStackTrace();
    log.error(e.getClass() + "\n" + e.getCause());
    return false;
}
//RedisUtil的get()方法
public String get(String key) {
        key = EncryptionUtil.encode(key + UtilConstant.ENCRYPT_SALT);
        //根据key获取redis中的value值
        return stringRedisTemplate.opsForValue().get(key);
    }
```

