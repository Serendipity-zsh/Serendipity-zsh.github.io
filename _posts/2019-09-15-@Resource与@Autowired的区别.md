---
layout: post
title: "@Resource与@Autowired的区别"
date: 2019-09-15
description: "@Resource,@Autowried,@Resource和@Autowired的区别"
tag: Spring
---

# @Resource 与 @Autowired 的区别

下面通过一个 springboot 的 demo 解释一下

demo环境：

```
IDE：IDEA
构建工具：Maven
java：java 8
```

项目结构：

[项目结构](https://github.com/Serendipity-zsh/Serendipity-zsh.github.io/raw/master/images/@Resource/project.png)

controller层代码：

```java
package serendipity.zsh.spring.resourceautowireddemo.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import serendipity.zsh.spring.resourceautowireddemo.service.FatherService;

import javax.annotation.Resource;

/**
 * @author Serendipity-zsh
 * @Date 2019/9/14 21:56
 */
@RestController
@RequestMapping("/api")
public class FatherController {

    @Resource
    private FatherService fatherService;

    @RequestMapping("/accedeWealth")
    public String accedeWealth() {
        return fatherService.accedeWealth();
    }
}
```

service层代码：

```java
//FathService接口
package serendipity.zsh.spring.resourceautowireddemo.service;

/**
 * @author Serendipity-zsh
 * @Date 2019/9/14 21:55
 */
public interface FatherService {
    /**
     * 继承财产
     * @return
     */
    String accedeWealth();
}
```

```java
//SonOneServiceImpl实现类
package serendipity.zsh.spring.resourceautowireddemo.service.impl;

import org.springframework.stereotype.Service;
import serendipity.zsh.spring.resourceautowireddemo.service.FatherService;

/**
 * @author Serendipity-zsh
 * @Date 2019/9/14 21:56
 */
@Service
public class SonOneServiceImpl implements FatherService {

    @Override
    public String accedeWealth() {
        return "SonOne：我要继承财富！";
    }
}

```

至此代码告一段落，启动项目，浏览器中输入：http://localhost:8080/api/accedeWealth

[第一次结果图](https://github.com/Serendipity-zsh/Serendipity-zsh.github.io/raw/master/images/@Resource/result.png)



## 第一次改动

将 FatherController 类中的 @Resource 注解替换为 @Autowired 注解，重启启动，浏览器显示的结果跟之前一样



## 第二次改动

增加一个FatherService接口的实现类：SonTwoServiceImpl

（第二个儿子赶来继承财产）

代码如下：

```java
package serendipity.zsh.spring.resourceautowireddemo.service.impl;

import serendipity.zsh.spring.resourceautowireddemo.service.FatherService;

/**
 * @author Serendipity-zsh
 * @Date 2019/9/14 21:57
 */
@Service
public class SonTwoServiceImpl implements FatherService {

    @Override
    public String accedeWealth() {
        return "SonTwo：我也要继承财产！";
    }
}
```

此时FatherController类的注解为**@Resource**

启动项目，报错：

```java
2019-09-14 22:36:01.132 ERROR 9886 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'fatherController': Injection of resource dependencies failed; nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type 'serendipity.zsh.spring.resourceautowireddemo.service.FatherService' available: expected single matching bean but found 2: sonOneServiceImpl,sonTwoServiceImpl
```

主要错误提示：expected single matching bean but found 2: sonOneServiceImpl,sonTwoServiceImpl

大致意思就是：被期望的单一结果匹配到两个结果（本来是让一个人继承财产，结果跑来两个要继承财产的）

解决方法：用 @Resource 注解的 name 属性或 @Qualifier 来指定一个合格的继承人（就是实现类）

修改 FatherController 类的代码：

```java
@Resource(name="SonOneServiceImpl")
private FatherService fatherService;
```

或者

```java
@Resource
@Qualifier("SonOneServiceImpl")
private FatherService fatherService;
```

现在我们已经指定了SonOne为合格的继承人（指定FatherService接口的实现类是SonOneServiceImpl）

启动项目，访问http://localhost:8080/api/acedeWealth

[第一次结果图](https://github.com/Serendipity-zsh/Serendipity-zsh.github.io/raw/master/images/@Resource/result.png)

## 第三次改动

在增加了SonTwoServiceImpl类的基础上，将FatherController类中的 @Resource注解修改为 @Autowired注解

启动项目，报错如下：

```java
Description:

Field fatherService in serendipity.zsh.spring.resourceautowireddemo.controller.FatherController required a single bean, but 2 were found:
	- sonOneServiceImpl: defined in file [/home/serendipity/IdeaProjects/Spring-Learning/resource-autowired-demo/target/classes/serendipity/zsh/spring/resourceautowireddemo/service/impl/SonOneServiceImpl.class]
	- sonTwoServiceImpl: defined in file [/home/serendipity/IdeaProjects/Spring-Learning/resource-autowired-demo/target/classes/serendipity/zsh/spring/resourceautowireddemo/service/impl/SonTwoServiceImpl.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed
```

主要错误提示：FatherController required a single bean, but 2 were found

大致意思就是：FatherController 需要一个 bean ，但是发现了两个（跟上次的错误差不多）

其实你把 @Resource 注解改成 @Autowired 注解时，IDEA应该就用 `红色波浪线` 提示你有错误了

解决方法：

1. 使用@Primary注解，在有多个实现 bean 时告诉Spring优先注入 @Primary 修饰的那个 bean 
2. 或者使用 @Qualifier 来标注需要注入的类（跟上次的一样）

用 @Primary 修饰实现类，告诉Spring，如果有多个实现类时，优先注入 @Primary 注解修饰的那个 bean，这里我们依据`长子继承财产的传统`（此处只是开玩笑举例子，没有任何其他意思），用 @Primary 注解把 SonOneServiceImpl 作为优先注入的bean，修改 SonOneServiceImpl 类的代码如下：

```java
package serendipity.zsh.spring.resourceautowireddemo.service.impl;

import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Service;
import serendipity.zsh.spring.resourceautowireddemo.service.FatherService;

/**
 * @author Serendipity-zsh
 * @Date 2019/9/14 21:56
 */
@Service
@Primary
public class SonOneServiceImpl implements FatherService {

    @Override
    public String accedeWealth() {
        return "SonOne：我要继承财产！";
    }
}
```

PS：这是你会发现 FatherController 类代码中刚才的 `红色波浪线` 消失了

启动程序，浏览器中输入：http://localhost:8080/api/accedeWealth

结果显示如下：

[第一次结果图](https://github.com/Serendipity-zsh/Serendipity-zsh.github.io/raw/master/images/@Resource/result.png)

正常显示预期结果

大致就说这些了，欢迎访问我的GItHub地址：

顺便源码地址奉上：