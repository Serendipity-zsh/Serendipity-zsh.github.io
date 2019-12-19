---
layout: post
title: SpringBoot 踩坑记录
description: 
date: 2019-06-12
tags: "SpringBoot"
---



# SpringBoot 踩坑记录



### Springfox 与 Hateoas冲突

```java
Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
22:30:34.331 logback [main] ERROR o.s.b.d.LoggingFailureAnalysisReporter - 

***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of method linkDiscoverers in org.springframework.hateoas.config.HateoasConfiguration required a single bean, but 15 were found:
	- modelBuilderPluginRegistry: defined in null
	- modelPropertyBuilderPluginRegistry: defined in null
	- typeNameProviderPluginRegistry: defined in null
	- documentationPluginRegistry: defined in null
	- apiListingBuilderPluginRegistry: defined in null
	- operationBuilderPluginRegistry: defined in null
	- parameterBuilderPluginRegistry: defined in null
	- expandedParameterBuilderPluginRegistry: defined in null
	- resourceGroupingStrategyRegistry: defined in null
	- operationModelsProviderPluginRegistry: defined in null
	- defaultsProviderPluginRegistry: defined in null
	- pathDecoratorRegistry: defined in null
	- relProviderPluginRegistry: defined by method 'relProviderPluginRegistry' in class path resource [org/springframework/hateoas/config/HateoasConfiguration.class]
	- linkDiscovererRegistry: defined in null
	- entityLinksPluginRegistry: defined by method 'entityLinksPluginRegistry' in class path resource [org/springframework/hateoas/config/WebMvcEntityLinksConfiguration.class]


Action:

Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed


Process finished with exit code 1
```

解决：

由于springfox 与Hateoas冲突

```xml
<!--     导入Swagger2的依赖　　-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.6.1</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.6.1</version>
        </dependency>
```



### Cannot resolve MVC View

```java
javax.servlet.ServletException: Could not resolve view with name 'hello' in servlet with name 'dispatcherServlet'
	at org.springframework.web.servlet.DispatcherServlet.render(DispatcherServlet.java:1353)
	at org.springframework.web.servlet.DispatcherServlet.processDispatchResult(DispatcherServlet.java:1118)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1057)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:943)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:634)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:741)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:526)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:139)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:343)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:408)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:861)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1579)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:748)
```

解决：

配置了数据返回格式为Json，但是Controller层没有设置正确的Json格式，导致MVC的视图渲染器(DispatcherServlet)无法对其正确解析

```java
//使用 @RestController
//或者使用 @Controller + @ResponseBody
```

