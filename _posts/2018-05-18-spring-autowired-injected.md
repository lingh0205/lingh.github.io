---
layout: post
current: post
cover:  assets/images/mysql.jpg
navigation: True
title: Spring Autowired Injected
date: 2018-05-19 18:00:00
tags: [Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: ghost
---

Hey! Welcome to Ghost, it's great to have you :)

We know that first impressions are important, so we've populated your new site with some initial **Getting Started** posts that will help you get familiar with everything in no time. This is the first one!

# Spring-Autowired-Injected

------

![](http://images2017.cnblogs.com/blog/743587/201709/743587-20170905175255272-402206582.png)

## @Autowired注解背后的工作原理
类内部的注解，如：@Autowired、@Value、@Required、@Resource以及EJB和WebSerivce相关的注解，是容器对Bean对象实例化和依赖注入时，通过容器中注册的Bean后置处理器处理这些注解的。

所以配置了上面这个配置，并且配置中打开了spring的扫描开关
```xml
<context:annotation-config />
```
将隐式地向Spring容器注册
> * AutowiredAnnotationBeanPostProcessor
> * CommonAnnotationBeanPostProcessor
> * RequiredAnnotationBeanPostProcessor
> * PersistenceAnnotationBeanPostProcessor

这4个专门用于处理注解的Bean后置处理器。

当 Spring 容器启动时，AutowiredAnnotationBeanPostProcessor 将扫描 Spring 容器中所有 Bean，当发现 Bean 中拥有@Autowired 注解时就找到和其匹配（默认按类型匹配）的 Bean，并注入到对应的地方中去。 

源码分析如下:
> * 通过 `AbstractAutowireCapableBeanFactory` 中的 `populateBean` 方法处理 `Autowired Bean` 的注入
> * 判断是否存在 `Autowired` 对象
boolean hasInstAwareBpps = this.hasInstantiationAwareBeanPostProcessors();
> * 获取 `Autowired` 对象的属性描述符
PropertyDescriptor[] filteredPds = this.filterPropertyDescriptorsForDependencyCheck(bw);
> * 调用 `AutowiredAnnotationBeanPostProcessor`.`postProcessPropertyValues` 方法进行属性的注入
pvs = ibp.postProcessPropertyValues((PropertyValues)pvs, filteredPds, bw.getWrappedInstance(), beanName);

属性注入源码如下：

```java
public PropertyValues postProcessPropertyValues(PropertyValues pvs, PropertyDescriptor[] pds, Object bean, String beanName) throws BeansException {
        InjectionMetadata metadata = this.findAutowiringMetadata(bean.getClass());

        try {
            metadata.inject(bean, beanName, pvs);
            return pvs;
        } catch (Throwable var7) {
            throw new BeanCreationException(beanName, "Injection of autowired dependencies failed", var7);
        }
}
```

> * 通过 `InjectionMetadata metadata = this.findAutowiringMetadata(bean.getClass());` 方法获取对应的Bean实例中property对应的属性信息
```java
private InjectionMetadata findAutowiringMetadata(Class<?> clazz) {
        InjectionMetadata metadata = (InjectionMetadata)this.injectionMetadataCache.get(clazz);
        if (metadata == null) {
            Map var3 = this.injectionMetadataCache;
            synchronized(this.injectionMetadataCache) {
                metadata = (InjectionMetadata)this.injectionMetadataCache.get(clazz);
                if (metadata == null) {
                    metadata = this.buildAutowiringMetadata(clazz);
                    this.injectionMetadataCache.put(clazz, metadata);
                }
            }
        }

        return metadata;
    }
```
> * 根据metadata信息进行属性的注入
```java
public void inject(Object target, String beanName, PropertyValues pvs) throws Throwable {
        if (!this.injectedElements.isEmpty()) {
            boolean debug = this.logger.isDebugEnabled();

            InjectionMetadata.InjectedElement element;
            for(Iterator var6 = this.injectedElements.iterator(); var6.hasNext(); element.inject(target, beanName, pvs)) {
                element = (InjectionMetadata.InjectedElement)var6.next();
                if (debug) {
                    this.logger.debug("Processing injected method of bean '" + beanName + "': " + element);
                }
            }
        }

}
```

## 注入Bean报错NullPointException原因总结
1）扫描器配置错误
2）父子容器问题
3）构造方法、构造代码库中使用注入的bean
4）new对象（未被spring托管）

### 问题一：扫描器配置错误
spring配置文件中必须打开对应的扫描开关，并且扫描路径包含对应的类
```xml
    <context:annotation-config/>
    <context:component-scan base-package="com.xxx.xxx"/>
```
web.xml中需配置spring初始化监听器，并指定配置文件路径
```xml
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
    
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```

### 问题二：Spring容器和springmvc父子容器问题
如下图所示：
![](https://images2015.cnblogs.com/blog/901523/201704/901523-20170414160346673-337189441.jpg)
Spring和springmvc和作为两个独立的容器，会把扫描到的注解对象分别放到两个不同的容器中，
Springmvc容器是spring容器的一部分，他们访问对象的范围如下所示：
> * springmvc子容器可以访问spring父容器中的对象
> * spring父容器不能访问springmvc子容器中的对象



### 问题三：构造函数、构造代码块执行顺序问题
spring容器管理的Bean是在构造函数执行结束后才调用对应的set方法进行Bean的注入的，因而在初始化的过程中使用对应的Bean对象，则会报NullPointException异常

解决策略如下：

在对应的类上面实现 `InitializingBean` 接口，复写 `afterPropertiesSet` 方法，此方法会在Bean属性注入结束后调用，从而避免NullPointException异常

```java
public class ExpressionManager implements InitializingBean {
    @Autowired
    ExpressionService expressionService;
    
    @Override
    public void afterPropertiesSet() throws Exception {
        reload();
    }
    
    protected void reload(){}
}
```

### 问题四：对象未被spring托管
使用对象为new创建的对象，spring容器无法管理，因而也无法实现依赖注入
