---
layout: post
current: post
cover:  assets/images/elasticsearch.jpg
navigation: True
title: Build ElasticSearch Site
date: 2018-02-08 18:00:00
tags: [Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: ghost
---

Below is just about everything you’ll need to style in the theme. Check the source code to see the many embedded elements within paragraphs.

# **[Elasticsearch索引内部原理最简洁的归纳](http://blog.csdn.net/lisi1129/article/details/79034067)**

------
### **Elasticsearch 内部索引写入原理**

> * 索引首先会写入到索引的 buffer缓存和translog日志文件中，这个期间不能被客户端索引；

> * 每隔一秒钟，buffer 缓存中的数据会被写入到新的segment缓存文件 file中，同时写入系统的缓存中os caching 中，并打开索引，外部客户端可以进行索引查询；这一点说明Elasticsearch 并不是所谓的实时索引，其中有一秒的延迟；

> * 写入segment file 后清空buffer缓存数据

> * 重复 1-3步骤的操作，新的segment不断增加，buffer不断清空；而transLog 的中的数据不断追加;

> * 当transLog 长度达到一定程度是时候，或者到达一定时间的时候，发生commit操作 

> * 发生commit操作的时候，所有的segment文件会被fsync强行刷到磁盘上；这个过程就是flush,默认是30分钟flush一次；或者tanslog过大的时候，也会触发flush .

> * 现有的transLog 被清空，创建一个新的transLog ；

步骤可以参考下图：
![Elasticsearch 内部索引写入原理](http://img.blog.csdn.net/20180111145751542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlzaTExMjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### **Elasticsearch 数据恢复原理**

> 在索引写入的过程中，只要segment缓存文件没有刷新到磁盘上，当elasticsearch集群出现宕机后，留住缓存中的数据都会丢失掉；当出现上面数据丢失的现象后，怎么处理呢；Elasticsearch有一套容错恢复规则；当数据出现丢失后，会根据transLog记录点重新生产segment缓存文件。

> 每一秒就是刷新Buffer，生成一个segment文件，导致文件过多，默认后台会执行segment merge 操作，在merge的时候，被标记为deleted 的document也会被彻底物理删除