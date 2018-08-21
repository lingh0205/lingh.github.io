---
layout: post
current: post
cover:  assets/images/mysql.jpg
navigation: True
title: MySQL Configuration optimize
date: 2018-08-10 19:10:00
tags: [Getting started]
class: post-template
subclass: 'post tag-getting-started'
author: LinGH
---

MySQL powers the most demanding Web, E-commerce, SaaS and Online Transaction Processing (OLTP) applications. It is a fully integrated transaction-safe, ACID compliant database with full commit, rollback, crash recovery and row level locking capabilities. MySQL delivers the ease of use, scalability, and performance to power Facebook, Google, Twitter, Uber, and Booking.com.


# MySQL 配置参数说明

------------

## sync_binlog 

MySQL提供一个sync_binlog参数来控制数据库的binlog刷到磁盘上去。

默认，`sync_binlog=0` ，表示MySQL不控制binlog的刷新，由文件系统自己控制它的缓存的刷新。这时候的性能是最好的，但是风险也是最大的。因为一旦系统Crash，在 `binlog_cache` 中的所有binlog信息都会被丢失。

如果 `sync_binlog>0` ，表示每 `sync_binlog` 次事务提交，MySQL调用文件系统的刷新操作将缓存刷下去。最安全的就是 `sync_binlog=1` 了，表示每次事务提交，MySQL都会把binlog刷下去，是最安全但是性能损耗最大的设置。这样的话，在数据库所在的主机操作系统损坏或者突然掉电的情况下，系统才有可能丢失1个事务的数据。但是binlog虽然是顺序IO，但是设置 `sync_binlog=1` ，多个事务同时提交，同样很大的影响MySQL和IO性能。虽然可以通过group commit的补丁缓解，但是刷新的频率过高对IO的影响也非常大。对于高并发事务的系统来说，“sync_binlog”设置为0和设置为1的系统写入性能差距可能高达5倍甚至更多。

所以很多MySQL DBA设置的 `sync_binlog` 并不是最安全的1，而是100或者是0。这样牺牲一定的一致性，可以获得更高的并发和性能。

调整方法：
```sql
    set global sync_binlog=10000;
```

------------

## innodb_flush_log_at_trx_commit

0：log buffer将每秒一次地写入log file中，并且log file的flush(刷到磁盘)操作同时进行。该模式下在事务提交的时候，不会主动触发写入磁盘的操作。  

1：每次事务提交时MySQL都会把log buffer的数据写入log file，并且 flush (刷到磁盘)中去，该模式为系统默认。  

2：每次事务提交时MySQL都会把log buffer的数据写入log file，但是 flush (刷到磁盘)操作并不会同时进行。该模式下，MySQL会每秒执行一次 flush(刷到磁盘)操作。  

当设置为0，该模式速度最快，但不太安全，mysqld 进程的崩溃会导致上一秒钟所有事务数据的丢失。
当设置为1，该模式是最安全的，但也是最慢的一种方式。在 mysqld 服务崩溃或者服务器主机 crash 的情况下，binary log 只有可能丢失最多一个语句或者一个事务。。
当设置为2，该模式速度较快，也比0安全，只有在操作系统崩溃或者系统断电的情况下，上一秒钟所有事务数据才可能丢失。

调整方法：
1) 找到mysql配置文件mysql.ini，修改成合适的值，然后重启mysql。
2) 动态调整

```sql
    set global innodb_flush_log_at_trx_commit=0;
```

------------

# 实践
磁盘性能较差时，可以调整 `sync_binlog` 和 `innodb_flush_log_at_trx_commit` 来降低刷盘频率，参考值如下：
```sql
mysql> set global sync_binlog=10000;
mysql> set global innodb_flush_log_at_trx_commit=0;
```
