---
layout: post
current: post
cover:  assets/images/dubbo.png
navigation: True
title: Feign-Permission-Denied
date: 2018-08-10 19:10:00
tags: [Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: LinGH
---

Feign is a declarative web service client. It makes writing web service clients easier. To use Feign create an interface and annotate it. It has pluggable annotation support including Feign annotations and JAX-RS annotations. 

# java.net.SocketException: Permission denied: connect

```text

java.net.SocketException: Permission denied: connect
	at java.net.DualStackPlainSocketImpl.connect0(Native Method)
	at java.net.DualStackPlainSocketImpl.socketConnect(DualStackPlainSocketImpl.java:83)
	at java.net.AbstractPlainSocketImpl.doConnect(AbstractPlainSocketImpl.java:339)
	at java.net.AbstractPlainSocketImpl.connectToAddress(AbstractPlainSocketImpl.java:200)
	at java.net.AbstractPlainSocketImpl.connect(AbstractPlainSocketImpl.java:182)
	at java.net.PlainSocketImpl.connect(PlainSocketImpl.java:172)
	at java.net.SocksSocketImpl.connect(SocksSocketImpl.java:392)
	at java.net.Socket.connect(Socket.java:579)
	at sun.net.NetworkClient.doConnect(NetworkClient.java:175)
	at sun.net.www.http.HttpClient.openServer(HttpClient.java:432)
	at sun.net.www.http.HttpClient.openServer(HttpClient.java:527)
	at sun.net.www.http.HttpClient.<init>(HttpClient.java:211)
	at sun.net.www.http.HttpClient.New(HttpClient.java:308)
	at sun.net.www.http.HttpClient.New(HttpClient.java:326)
	at sun.net.www.protocol.http.HttpURLConnection.getNewHttpClient(HttpURLConnection.java:997)
	at sun.net.www.protocol.http.HttpURLConnection.plainConnect(HttpURLConnection.java:933)
	at sun.net.www.protocol.http.HttpURLConnection.connect(HttpURLConnection.java:851)
	at sun.net.www.protocol.http.HttpURLConnection.getOutputStream(HttpURLConnection.java:1092)
	at cn.richinfo.common.utils.HttpRequest.GetResponseString(HttpRequest.java:231)
	at cn.richinfo.spring.handler.RequestHandler.postRequest(RequestHandler.java:41)
	at cn.richinfo.spring.service.SendFlowPresentService.sendRequest(SendFlowPresentService.java:41)
	at cn.richinfo.spring.service.SendFlowPresentService.edit(SendFlowPresentService.java:67)
	at cn.richinfo.spring.service.SendFlowPresentService$$FastClassBySpringCGLIB$$b5535fe9.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invoke(MethodProxy.java:204)
	at org.springframework.aop.framework.CglibAopProxy$CglibMethodInvocation.invokeJoinpoint(CglibAopProxy.java:720)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:157)
	at org.springframework.transaction.interceptor.TransactionInterceptor$1.proceedWithInvocation(TransactionInterceptor.java:99)
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:281)
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:96)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:179)
	at org.springframework.aop.framework.CglibAopProxy$DynamicAdvisedInterceptor.intercept(CglibAopProxy.java:655)
	at cn.richinfo.spring.service.SendFlowPresentService$$EnhancerBySpringCGLIB$$7fbe26db.edit(<generated>)
	at cn.richinfo.spring.handler.SendFlowPresentHandler.run(SendFlowPresentHandler.java:38)
```

## Cause  
This is a known bug in Java 7, as per this post.  
This can be caused by anti-virus or firewall software blocking Java from connecting to the Server port.  

## Resolution
Add `-Djava.net.preferIPv4Stack=true` to the `FISHEYE_OPTS` environment variable to help enable support for IPv4 on Java 7.  
Change the anti-virus or firewall software so that Java can connect to the server on the specified port.  


