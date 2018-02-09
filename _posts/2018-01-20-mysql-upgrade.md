# **MySQL版本升级**

------
## **[Changes in MySQL 5.7.14 (2016-07-29, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-14.html)**

### **编译提醒**

>CMake支持被添加到Developer Studio 12.5编译。 （错误＃82249，错误＃24303829，错误＃81274，错误＃23212938）

### **性能架构注释**

> 现在不推荐使用 `INFORMATION_SCHEMA INNODB_LOCKS` 和 `INNODB_LOCK_WAITS` 表，以便在提供替换的性能模式表的MySQL 8.0中进行删除。

### **sys模式注释**

> * sys模式现在有一个`quote_identifier()`函数，给定一个字符串参数，生成一个适合包含在SQL语句中的带引号的标识符。 （错误＃22011361，错误＃78823）

> * `sys schema diagnostics()`过程的输出现在包含`mysql.slave_master_info`系统表中的`Tls_version`列。

### **功能添加或更改**

> RHEL/OEL和Fedora发行版的RPM软件包的％全局compatver值从5.6.25更新为5.6.31。 （Bug＃23038018）

> **参考文献：**另见：错误＃22980983。

> 为`--port`选项（例如，`--port =`，`--port =“”`）指定一个空值现在被弃用，并导致警告。在MySQL 8.0中，一个空值将是一个错误。 （Bug＃23023457，Bug＃80903）

## **[Changes in MySQL 5.7.15 (2016-09-06, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-15.html)**

### **安全说明**

> `validate_password`插件现在支持拒绝与当前会话用户名匹配的密码，无论是正向还是反向。要启用对此功能的控制，插件会公开一个`validate_password_check_user_name`系统变量。默认情况下，该变量被禁用;默认情况下会更改为在MySQL 8.0中启用。有关更多信息，请参阅密码验证插件选项和变量。

### **功能添加或更改**

> **InnoDB：**可以使用新的动态配置选项`innodb_deadlock_detect`来禁用死锁检测。在高并发系统上，当大量线程等待同一个锁时，死锁检测会导致速度下降。有时，在发生死锁时，禁用死锁检测并依赖`innodb_lock_wait_timeout`设置进行事务回滚可能更有效。 （Bug＃23477773）

> 单元文件（`mysqld_pre_systemd`）的systemd支持脚本现在仅在其位置与模式`/var/log/mysql*.log`匹配时才会帮助创建错误日志文件。在其他情况下，错误日志目录必须是可写的，或者对于运行`mysqld`进程的用户，错误日志必须是可写的。此外，`mysqld_pre_systemd`现在可以避免创建不安全的临时文件。 （Bug＃24516262）

> 已经添加了`CMake WITH_LZ4`选项来控制在编译过程中使用哪个LZ4库。默认情况下，将WITH_LZ4设置为绑定，因此使用MySQL提供的库。将`WITH_LZ4`设置为系统将使用操作系统中的LZ4库。 （错误＃23607230）

## **[Changes in MySQL 5.7.16 (2016-10-12, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-16.html)**

### **弃用和删除说明**

> 随着MySQL 8.0中的数据字典的引入，`--ignore-db-dir`选项和`ignore_db_dirs`系统变量变得多余，并在该版本中被删除。 因此，它们现在在MySQL 5.7中被弃用了。

### **安全说明**

> **不兼容的更改：**对于`STANDALONE`和`WIN`构建，缺省的`secure_file_priv`值已从空字符串更改为NULL。这是一个安全的默认设置，因为它禁用导入和导出操作。要允许这些操作，请将secure_file_priv设置为用于这些操作的目录的路径名。 （Bug＃24679907，Bug＃24695274，Bug＃24707666）

> MySQL商用服务器链接的OpenSSL库已经更新到版本1.0.1u。有关此版本中已解决问题的说明，请参阅[http://www.openssl.org/news/vulnerabilities.html](http://www.openssl.org/news/vulnerabilities.html)。

> 此更改不会影响Oracle生成的MySQL服务器的MySQL社区版本，而是使用yaSSL库。 （Bug＃24753389）

> yaSSL升级到版本2.4.2。此升级解决了以下问题：潜在的AES侧通道泄漏; DSA填充不寻常的大小; `SSL_CTX_load_verify_locations()` OpenSSL兼容性函数无法处理长路径目录名称。 （Bug＃24512715，错误＃24740291）

## **[Changes in MySQL 5.7.17 (2016-12-12, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-17.html)**

### **编译提醒**

> 对于高于4.4的GCC版本，`-fno-expensive-optimizations`被替换为`-ffp-contract = off`，这可以实现更多的优化。 感谢Alexey Kopytov的补丁。 （错误＃24571672，错误＃82760）

### **MySQL企业笔记**

> MySQL企业版的企业加密现在使服务器管理员可以通过设置环境变量对最大密钥长度施加限制。 这些可以用来防止客户端通过将密钥长度传递给密钥生成操作来使用过多的CPU资源。 有关更多信息，请参阅企业加密使用情况和示例。 （错误＃19687742）

### **打包说明**

> RPM套件现在使用`-DWITH_NUMA = ON`构建，对于支持NUMA的平台：OEL高于EL5，Fedora，SLES和Docker。 （错误＃24689078）

### **安全说明**

> **不兼容的更改：**

> 这些更改是对`mysqld_safe`进行的：

> 在`mysqld_safe`中不安全地使用`rm`和`chown`会导致权限升级。现在只能在目标目录是`/var/log`的情况下使用`chown`。不兼容的变化是，如果`Unix`套接字文件的目录丢失，它不再被创建;相反，发生错误。由于这些更改，`/bin/bash`需要在`Solaris`上运行`mysqld_safe`。 `/bin/sh`仍然在其他`Unix/Linux`平台上使用。

> 现在只有在命令行上才能接受`--ledir`选项，而不是在选项文件中。

> `mysqld_safe`忽略当前的工作目录。

> **其他相关更改：**

> 调用`mysqld_safe`的初始化脚本显式地传递`--basedir`。

> 只有基本目录是`/var/log`或`/var/lib`，初始化脚本才会创建错误日志文件。

> 未使用的SLES系统文件被删除。（Bug＃24483092，Bug＃25088048，Bug＃25378439，Bug＃25378565）

> 参考文献：另见：错误＃24464380，错误＃24388753，错误＃24619033，错误＃82920。

> MySQL服务器现在包含一个插件库，使管理员能够在连续失败连续尝试一定数量的连接之后，向客户端引入服务器响应的延迟。此功能提供了一种阻止尝试访问MySQL用户帐户的暴力攻击。有关更多信息，请参阅连接控制插件。

> OpenSSL将于2016年12月结束对版本1.0.1的支持;请参阅[https://www.openssl.org/policies/releasestrat.html](https://www.openssl.org/policies/releasestrat.html)。因此，MySQL商业服务器版本现在使用版本1.0.2而不是版本1.0.1，并且用于MySQL商业服务器的链接的OpenSSL库已经从版本1.0.1更新到版本1.0.2j。有关此版本中已解决问题的说明，请参阅[https://www.openssl.org/news/vulnerabilities.html](https://www.openssl.org/news/vulnerabilities.html)。

> 此更改不会影响Oracle生成的MySQL服务器的MySQL社区版本，而是使用yaSSL库。



### **功能添加或更改**


> * 不兼容的变化;分区：MySQL服务器中的通用分区处理程序已被弃用，并将在MySQL 8.0中被删除。作为此更改的一部分，`mysqld --partition`和`--skip-partition`选项以及`-DWITH_PARTITION_STORAGE_ENGINE`构建选项也将被弃用，并且稍后将被删除;分区将不再显示在`INFORMATION_SCHEMA.PLUGINS`表或`SHOW PLUGINS`的输出中。

> 除去泛型分区处理程序后，用于给定表的存储引擎将会像InnoDB和NDB存储引擎当前那样提供自己的（“本地”）分区处理程序。目前，没有其他MySQL存储引擎提供本地分区支持，也没有计划用于当前或开发版本的MySQL中的任何其他存储引擎。

> 使用带有非本地分区的表现在会导致`ER_WARN_DEPRECATED_SYNTAX`警告。此外，服务器在启动时执行检查以识别使用非本地分区的表;对于任何找到的，服务器将消息写入其错误日志。要禁用此检查，请使用`--disable-partition-engine-check`选项。

> 为了准备迁移到MySQL 8.0，任何包含非本地分区的表都应该改为使用提供本地分区的引擎，或者不要分区。例如，要将表更改为InnoDB，请执行以下语句：

```sql
ALTER TABLE table_name ENGINE = INNODB;
```

> * InnoDB：默认情况下，InnoDB在计算统计信息时读取未提交的数据。对于从表中删除行的未提交事务，InnoDB排除在计算行估计和索引统计信息时被删除标记的记录，这可能导致对在表上并行使用的其他事务执行非最佳执行计划除`READ UNCOMMITTED`以外的事务隔离级别。为了避免这种情况，可以启用一个新的配置选项`innodb_stats_include_delete_marked`，以确保InnoDB在计算持久化优化器统计信息时包含删除标记的记录。 （Bug＃23333990） 

> * `mysqld`的`systemd`服务文件现在在[Unit]部分包含一个Documentation值，以提供到MySQL参考手册中systemd文档的链接。 （Bug＃24735762）


> * 单元测试现在使用Google Mock 1.8。 （错误＃24572381，错误＃82823）


> * 如果使用`--daemonize`调用`mysqld`，则如果连接到终端类型的设备，stdout和stderr会被重定向到`/dev/null`，以便`mysqld`可以像真守护程序那样工作。 （Bug＃21627629）
 

> * MySQL组复制是一个新的MySQL插件，使您能够在一组MySQL服务器实例之间创建高度可用的分布式MySQL服务，并具有数据一致性，冲突检测和解决以及所有内置的组成员服务。通过使用功能强大的新组通信服务，该服务提供了流行的Paxos算法的实现，MySQL服务器实例组自动协调数据复制，一致性和成员身份。这提供了使MySQL数据库高度可用的所有内置机制。

>  默认情况下，组复制以单主模式运行，其中一个称为主服务器的服务器实例接受写请求。组中剩余的服务器实例（称为辅助服务器实例）充当主服务器的副本。在小学发生意外失败的情况下，自动进行初选，其中一个辅助选举为新的小学。组复制还支持虚拟同步多主复制，并具有一定的注意事项和限制，它提供无处不在的功能。在这种模式下，所有成员都是平等的，您可以在组中的所有MySQL服务器实例上分配您的读写。

>  无论操作模式如何，组复制都提供了动态成员资格服务，该服务依赖于分布式故障检测。服务器实例可以动态地加入和离开组，并且可以通过性能模式表在任何时候查询组的成员列表。加入组的服务器实例通过执行自动时间点恢复来自动将其状态与组同步，从而确保它们与组同步。

>  使用InnoDB存储引擎，性能模式表，标准GTID和众所周知的复制基础结构（二进制和中继日志，多源复制，多线程从属执行），MySQL组复制的虚拟同步复制也是MySQL的完全集成部分。等等），这使得它对现有的MySQL用户来说是一个熟悉和直观的体验，并且使其与MySQL的标准异步和半同步复制相结合变得非常容易，允许您根据需要进行混合和匹配，以创建各种复杂的复制拓扑。


## **[Changes in MySQL 5.7.18 (2017-04-10, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-18.html)**

### **编译提醒**

> Windows现在使用默认的运行时库（构建使用/ MD标志）。 （Bug＃25611609）

> CMake支持被添加到Developer Studio 12.6编译。 （Bug＃25384295）

### **配置说明**

> 如果同时给出`-DENABLE_DEBUG_SYNC = OFF`和`-DWITH_DEBUG = ON`，MySQL将无法编译。 `ENABLE_DEBUG_SYNC`选项已被删除，并启用`WITH_DEBUG`启用调试同步。 （Bug＃18374703）

### **弃用和删除说明**

> `PROCEDURE ANALYZE()`语法现在已被弃用，并在MySQL 8.0中被删除。

> `--temp-pool`服务器选项已被弃用，并将在MySQL 8.0中被删除。

> DTrace的支持已被弃用，并在MySQL 8.0中被删除。

### **打包说明**

> Microsoft Windows：提醒：MySQL 5.7要求Microsoft Visual C ++ 2013 Redistributable Package在Windows平台上运行。在启动服务器之前，用户应确保软件包已安装在系统上。该软件包位于Microsoft下载中心。

> RPM软件包结构的变化要求删除更大的一组软件包，以便干净地安装MySQL服务器。 （Bug＃25603087）

> 为了避免潜在的竞争条件，Debian软件包现在使用GNU安装实用程序，而不是`mkdir`，`touch`和`chown`的组合。 （Bug＃25258829）

> `my-default.cnf.sh`文件（用于生成默认的`my-default.cnf`或`my-default.ini`文件）不再包含在源代码发行版中，`my-default.cnf`和`my-default.ini`不再包含在分发包中或由分发包安装。 （Bug＃22525354）

### **安全说明**

> MySQL商用服务器链接的OpenSSL库已经更新到版本1.0.2k。 在[http://www.openssl.org/news/vulnerabilities.html](http://www.openssl.org/news/vulnerabilities.html)上描述了在新的OpenSSL版本中修复的问题。

> 此更改不会影响Oracle生成的MySQL服务器的MySQL社区版本，而是使用yaSSL库。 （错误＃25768671，错误＃25615451，CVE-2016-7055，CVE-2017-3731，CVE-2017-3732）

> `keyring_okv`插件不再支持RSA或DSA密钥类型。 （Bug＃25540639）

> 现在，`keyring_okv`密钥环插件可以使用SafeNet KeySecure应用装置作为密钥环存储的KMIP后端。 有关说明，请参阅使用keyring_okv KMIP插件。

### **线程池注释**

> 为了改善thread_pool插件的性能，连接认证和初始化已经从接受者线程移动到处理客户端连接的线程池工作线程。 这使得接受者线程能够以更高的速率处理新的连接，同时减少了延迟。 INFORMATION_SCHEMA TP_THREAD_GROUP_STATE表现在包含一个CONNECT_THREAD_COUNT列，它表示正在处理或等待处理连接初始化和认证的线程数。 每个线程组最多可以有四个连接线程; 这些线程在一段时间不活动之后过期。 （错误＃17159742）

### **功能添加或更改**
> 复制：现在可以将来自“组复制”组的查看更改事件复制到DATABASE类型的外部多线程从站（MTS）。 （Bug＃25170698，错误＃84008）

> 复制：当向`WAIT_UNTIL_SQL_THREAD_AFTER_GTIDS()`提供负数或小数超时参数时，服务器的行为将以意想不到的方式进行。有了这个修复：

> 小数超时值按原样读取，不进行四舍五入。

> 如果服务器处于严格的SQL模式，否定的超时值将被拒绝并显示错误;如果服务器不处于严格的SQL模式，则该值会使该函数立即返回NULL，而不会等待，然后发出警告。（Bug＃24976304，错误＃83537）

> 如果MySQL配置为使用`-Wno-error`选项生成，则`mysql_config`会为其`--cflags`选项生成不正确的输出。

> `mysql_config`和`pkg-config`生成的一组编译器选项现在由白名单而不是黑名单决定。 （Bug＃25040566，Bug＃22898475，Bug＃80662）

> UTF-8二进制排序规则的性能得到了改善。 （Bug＃24788778，Bug＃83247，Bug＃25076862）

> Debian/Ubuntu包装维护者脚本的一致性和可维护性得到了改进。 （错误＃23588977）

> `mysql_secure_installation`对于它认为是有效的和没有响应是更严格的。 （错误＃13344753，错误＃62925）

> 替换实用程序已被弃用，并将在MySQL8.0中被删除。如果您希望继续使用此实用程序，请确保保留已安装版本的MySQL的副本。


## **[Changes in MySQL 5.7.19 (2017-07-17, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-19.html)**

### **账户管理**

> 在数据目录初始化或升级期间，MySQL现在创建一个“mysql.session'@localhost”保留帐户。 该帐户由插件内部使用来访问服务器。 它被锁定，因此它不能用于客户端连接。 （Bug＃25642343）

### **弃用和删除说明**

InnoDB：`innodb_undo_logs`配置选项已弃用，将在未来版本中删除。 `innodb_rollback_segments`配置选项执行相同的功能，应该使用。

`Innodb_available_undo_logs`状态变量已弃用，并将在未来版本中删除。

`libmysqld`嵌入式服务器库已被弃用，并将在MySQL 8.0中被删除。 这些也被弃用，将被删除：

> * `mysql_config --libmysqld-libs`，`--embedded-libs`和`--embedded`选项

> * `CMake WITH_EMBEDDED_SERVER`，`WITH_EMBEDDED_SHARED_LIBRARY`和`INSTALL_SECURE_FILE_PRIV_EMBEDDEDDIR`选项

> * （无证）`mysql --server-arg`选项

> * `mysqltest` - 嵌入式服务器， - 服务器参数和 - 服务器文件选项

> * `mysqltest_embedded`和`mysql_client_test_embedded`测试程序

### **包装说明**

> mysqladmin被添加到Docker / Minimal包中，因为它是InnoDB集群所需要的。 （Bug＃25998285）

> Debian / Ubuntu软件包现在支持systemd的多个MySQL实例。 请参阅使用systemd管理MySQL服务器。 （错误＃24559588，错误＃82785）

### **安全说明**

> 用于MySQL商业服务器的链接OpenSSL库已更新至版本1.0.21。 > 在http://www.openssl.org/news/vulnerabilities.html上描述了在新的OpenSSL版本中修复的问题。

> 此更改不会影响Oracle生成的MySQL服务器的MySQL社区版本，而是使用yaSSL库。 （错误＃26160962）

> MySQL企业版现在支持MySQL用户的LDAP可插入认证。 这使得MySQL服务器可以使用LDAP（轻量级目录访问协议）通过访问目录服务（如X.500）来认证MySQL用户。 有关更多信息，请参阅可插入的身份验证。

> MySQL企业版现在包含一个keyring_aws插件，它与亚马逊网络服务密钥管理服务进行通信，作为密钥生成的后端，并使用本地文件存储密钥。 有关更多信息，请参阅MySQL Keyring。

### **几何参数支持**

这些函数以前接受WKB字符串或几何参数。 几何参数的使用现在被弃用，并产生一个警告。 几何参数将不被MySQL 8.0接受。

> * ST_GeomCollFromWKB(), ST_GeometryCollectionFromWKB()

> * ST_GeomFromWKB(), ST_GeometryFromWKB()

> * ST_LineFromWKB(), ST_LinestringFromWKB()

> * ST_MLineFromWKB(), ST_MultiLinestringFromWKB()

> * ST_MPointFromWKB(), ST_MultiPointFromWKB()

> * ST_MPolyFromWKB(), ST_MultiPolygonFromWKB()

> * ST_PointFromWKB()

> * ST_PolyFromWKB(), ST_PolygonFromWKB()

有关将引用这些函数的查询从使用几何参数迁移到使用WKB参数的信息，请参阅从WKB值创建几何值的函数。

### **功能添加或更改**

> * 复制：添加了`group_replication_transaction_size_limit`变量，使您能够保护组，防止大事务导致失败。 （Bug＃84785，Bug＃25510757）

> * 复制：组复制现在支持`SAVEPOINT SQL`事务。

> * `RPM .spec`文件现在包含对运行单元测试的支持。 （错误＃25814143，错误＃85743）

> * 对于Windows，MSI安装程序包现在包含对所需Visual Studio可再发行组件包的检查，并生成一条消息，要求用户在缺失的情况下进行安装。 （Bug＃25658832）

> * mysql客户端现在支持使用十六进制符号（0xvalue）显示二进制数据的`--binary-as-hex`选项。 感谢Daniëlvan Eeden的补丁。 （错误＃25340722，错误＃84391）

> * audit_log过滤器解析器现在为过滤器定义中的意外JSON元素生成错误。 （Bug＃24360663）

### **[Changes in MySQL 5.7.20 (2017-10-16, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html)**

### **审计日志注释**

> audit_log插件的事件匹配过滤器规则现在支持abort元素，该元素可用于防止执行限定事件。 有关更多信息，请参阅审计日志过滤。 例如，可以使用此功能来增强MySQL企业防火墙的功能，通过编写符合语句的审计筛选规则，并基于语句本身的特征来阻止这些规则，从而在每个用户的基础上阻止SQL语句。

### **弃用和删除说明**

> * 以前，--transaction-isolation和--transaction-read-only服务器启动选项对应于tx_isolation和tx_read_only系统变量。 为了启动选项和系统变量名称之间的更好的名称对应，transaction_isolation和transaction_read_only已被创建为tx_isolation和tx_read_only的别名。 tx_isolation和tx_read_only变量现在已被弃用，并将在MySQL 8.0中被删除。 应该调整应用程序以使用transaction_isolation和transaction_read_only。

> * 查询缓存现已被弃用，并在MySQL 8.0中被删除。 弃用包括这些项目：

```
FLUSH QUERY CACHE和RESET QUERY CACHE语句。

SQL_CACHE和SQL_NO_CACHE SELECT修饰符。

这些系统变量：
have_query_cache，
ndb_cache_check_time，
query_cache_limit，
query_cache_min_res_unit，
query_cache_size，
query_cache_type，
query_cache_wlock_invalidate

这些状态变量：
Qcache_free_blocks，
Qcache_free_memory，
Qcache_hits，
Qcache_inserts，
Qcache_lowmem_prunes，
Qcache_not_cached，
Qcache_queries_in_cache，
Qcache_total_blocks
```
> * 默认情况下，mysql客户端会在发送到服务器的语句中删除注释，并使用--skip-comments（strip注释）和--comments（保留注释）来控制此行为。
>  评论剥离现已被弃用。 这个功能和控制它的选项将会在未来的MySQL版本中被删除。

> * 这些针对表和列引用的语法结构现在已被弃用，并且将在未来版本的MySQL中被删除。 这些结构的实例应该改变以消除前导期。

```
.col_name
.tbl_name
.tbl_name.col_name
```

### **安装说明**

> * 对于使用systemd的平台（[请参阅使用systemd管理MySQL服务器](https://dev.mysql.com/doc/refman/5.7/en/using-systemd.html)），数据目录在服务器启动时如果为空，则进行初始化。 如果数据目录是暂时消失的远程挂载，则这可能是一个问题：挂载点将显示为空的数据目录，然后将其初始化为新的数据目录。 现在可以抑制这种自动初始化行为。 在/ etc / sysconfig / mysql文件中指定以下行（如果该文件不存在，请创建该文件）：
>
>    NO_INIT=true

### **包装说明**

> * MySQL服务器Docker镜像中缺少`mysqlcheck`，导致`mysql_upgrade`无法运行。 （错误＃26400146，错误＃86968）

### **安全说明**

> * `mysqld`和`mysql_ssl_rsa_setup`自动生成的证书现在使用X509 v3而不是v1。 （Bug＃26521654）

> * `keyring_okv`插件现在支持密码保护用于安全连接的密钥文件。 请参阅[使用keyring_okv KMIP插件](https://dev.mysql.com/doc/refman/5.7/en/keyring-okv-plugin.html)。

### **功能添加或更改**

> * 复制：在发行`STOP GROUP_REPLICATION`的以前版本中停止了插件，但服务器仍然接受事务。 这意味着交易没有转交给集团。 为了使`STOP GROUP_REPLICATION`更安全，现在在发出`STOP GROUP_REPLICATION`时立即将`super_read_only`设置为ON，这确保了不接受事务。 （错误＃25495067，错误＃84795）

> * 复制：添加了`group_replication_member_weight`变量，使您可以控制在单主模式下选择新的原色。 在以前的版本中，主要选举是基于成员的UUID，在故障转移的情况下选择最低的UUID作为新的主要UUID。 使用此变量为成员分配数字权重，以确保选择特定成员，例如在主计划的维护期间或确保某些硬件被优先化。

## [Changes in MySQL 5.7.21 (2018-01-15, General Availability)](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html)

### **审计日志注释**

> MySQL企业审计现在支持审计日志文件的压缩和加密。 加密基于用户定义的密码。 要使用此功能，必须启用MySQL密钥环，因为审计日志记录将其用于密码存储。 除了现有的XML格式之外，MySQL企业审计现在还支持JSON格式的日志记录。 对于JSON格式，可以使用提供运行时日志读取功能的函数。 有关更多信息，请参阅MySQL企业审计。


> ![](https://www.elastic.co/guide/en/elasticsearch/guide/current/images/icons/note.png)
> 与之前的MySQL版本相比，日志文件名称（audit_log_file系统变量值）的解释已经改变，在审计日志插件初始化和终止时日志文件重命名行为也已经改变。 请参阅审计日志文件名称。


### **配置说明**

> * 对于RHEL，SLES和Fedora RPM，调试版本的默认插件目录已经从`/usr/lib64/mysql/plugin`更改为`/usr/lib64/mysql/plugin/debug`。 （Bug＃27072155，Bug＃88363）

> * MySQL企业审计和MySQL企业防火墙的安装脚本现在在InnoDB而不是MyISAM表中创建它们在MySQL系统数据库中的关联表。 （Bug＃26323351，Bug＃26906601）

> * 内存映射事务协调器的8KB硬编码内存页面大小对于诸如ARM64和PowerPC等页面大小更大的平台而言太小。 服务器现在调用系统调用来获取当前平台的页面大小，而不是使用硬编码值。 `--log-tc-size`选项的结果是最小值和默认值现在是页面大小的6倍。 此外，该值必须是页面大小的倍数。 感谢Alexey Kopytov的补丁。 （Bug＃23014086，Bug＃80818，Bug＃26931470，Bug＃87995）

### **弃用和删除说明**

> InnoDB：innodb_undo_tablespaces配置选项已弃用，将在未来版本中删除。

### **性能架构注释**

> 性能架构`setup_timers`表现在已经被弃用了，在MySQL 8.0中被删除，就像在`performance_timers`表中的TICK行一样。

### **可插入的身份验证**

> * 对于LDAP认证插件，处理由`authentication_ldap_sasl_group_search_attr`和`authentication_ldap_simple_group_search_attr`系统变量指示的组搜索属性更加灵活。 如果组搜索属性是`isMemberOf`，则LDAP身份验证将直接检索用户属性`isMemberOf`值并将其分配为组信息。 如果组搜索属性不是`isMemberOf`，则LDAP认证会搜索用户所属的所有组。 （后者是默认行为）。这种行为基于如何以两种方式存储LDAP组信息：1）组条目可以具有名为`memberUid`的属性或具有用户名值的成员; 2）用户条目可以具有名为`isMemberOf`的属性，其值是组名。 （错误＃26317645）
> * LDAP身份验证插件现在允许提供用户DN信息的身份验证字符串以`+`字符开头。 在没有这个字符的情况下，认证字符串值按原样处理，如前所述。 如果身份验证字符串以`+`开头，则插件会将帐户用户名称中的完整用户DN值与身份验证字符串（+删除）一起构造为`cn`属性值。 认证字符串按照`mysql.user`系统表中给出的方式存储，在认证之前动态构建完整的用户DN。

这个帐号认证字符串在开始时没有+，所以作为完整的用户DN：

```sql
CREATE USER 'admin'
  IDENTIFIED WITH authentication_ldap_simple
  BY "cn=admin,ou=People,dc=example,dc=com";
```

这个账户认证字符串在开始处具有+号，因此它仅被视为完整用户DN的一部分：

```sql
CREATE USER 'accounting'
  IDENTIFIED WITH authentication_ldap_simple
  BY "+ou=People,dc=example,dc=com";
```

在这种情况下，完整的用户DN使用accounting作为cn属性连同认证字符串一起构造，以产生“cn = accounting，ou = People，dc = example，dc = com”。 （Bug＃26147775）

对于LDAP认证插件，组搜索属性是固定的，不可配置的。 两个新的系统变量现在可以使用自定义组过滤器：authentication_ldap_sasl_group_search_filter和authentication_ldap_simple_group_search_filter。 （错误＃26091340）


### **安全说明**

> * 不兼容的更改：对于sha256_password身份验证插件，现在将密码限制为最多256个字符;对于old_passwords = 2，密码限制为PASSWORD（）函数。而且，密码哈希轮次的数量被限制，以限制使用的CPU时间。 （Bug＃27099029，Bug＃27194270）

> * 用于MySQL商业服务器的链接OpenSSL库已更新至版本1.0.2n。在http://www.openssl.org/news/vulnerabilities.html上描述了在新的OpenSSL版本中修复的问题。

> * 此更改不会影响Oracle生成的MySQL服务器的MySQL社区版本，而是使用yaSSL库。 （Bug＃27212666，Bug＃27236394）

> * MySQL现在支持底层密匙环密钥库之间的密钥迁移。这使DBA能够将MySQL安装从一个密钥环插件切换到另一个。请参阅在Keyring密钥库之间迁移密钥。

> * MySQL Enterprise Edition现在包含一个keyring插件keyring_encrypted_file，它与keyring_file插件在使用本地数据文件进行密钥存储时类似，但也可以根据用户定义的密码对文件进行加密。请参阅使用keyring_encrypted_file Keyring插件。

### **功能添加或更改**

> * 复制：

> 现在可以使用[group_replication_ip_whitelist](https://dev.mysql.com/doc/refman/5.7/en/group-replication-options.html#sysvar_group_replication_ip_whitelist)系统变量将主机名称指定为组复制连接的白名单的一部分。主机名称支持CIDR表示法。不支持解析为IPv6地址的主机名。
>
> 对于主机名称，名称解析仅在另一个服务器发出连接请求时发生。无法解析的主机名不会被视为白名单验证，并且会向错误日志中写入警告消息。对已解析的主机名执行前向确认的反向DNS（FCrDNS）验证。  
> ![](https://www.elastic.co/guide/en/elasticsearch/guide/current/images/icons/note.png): 
> 主机名本来就不如白名单中的IP地址安全。 FCrDNS验证提供了良好的保护级别，但可能会受到某些类型的攻击。仅在严格需要时才在白名单中指定主机名称，并确保用于名称解析的所有组件（如DNS服务器）均受您的控制。您还可以使用主机文件在本地实施名称解析，以避免使用外部组件。



> * 复制：[group_replication_allow_local_disjoint_gtids_join](https://dev.mysql.com/doc/refman/5.7/en/group-replication-options.html#sysvar_group_replication_allow_local_disjoint_gtids_join)系统变量已被弃用，并计划在未来的版本中删除。

> * [-DWITH_ASAN_SCOPE](https://dev.mysql.com/doc/refman/5.7/en/source-configuration-options.html#option_cmake_with_asan_scope) CMake选项启用AddressSanitizer `-fsanitize-address-use-scope-clang`标志，以便在范围内检测后使用。默认是关闭的。要使用此选项，还必须启用[-DWITH_ASAN](https://dev.mysql.com/doc/refman/5.7/en/source-configuration-options.html#option_cmake_with_asan)。 （Bug＃27095089）


