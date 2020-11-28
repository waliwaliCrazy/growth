# mysql 常见优化方案

---

- [mysql 常见优化方案](#mysql-常见优化方案)
- [1 优化数据库结构](#1-优化数据库结构)
  - [1.1 优化表的数据类型](#11-优化表的数据类型)
    - [1.1.1 选择优化的数据类型](#111-选择优化的数据类型)
    - [1.1. 整数类型](#11-整数类型)
    - [1.2. 实数类型](#12-实数类型)
  - [1.2 拆分表，提供访问效率](#12-拆分表提供访问效率)
  - [1.3 逆范式](#13-逆范式)
  - [1.4 使用冗余统计表](#14-使用冗余统计表)
  - [1.5 选择更合适的表类型](#15-选择更合适的表类型)
- [2. 定位慢SQL](#2-定位慢sql)
- [3. 通过 EXPLAIN 分析执行计划](#3-通过-explain-分析执行计划)
- [4. 索引](#4-索引)
- [5. 其他优化措施](#5-其他优化措施)
- [参考](#参考)
- [附录](#附录)
  - [show status 相关](#show-status-相关)

---

# 1 优化数据库结构

## 1.1 优化表的数据类型

`MySQL` 支持的数据类型非常多，选择正确的数据类型对获得高性能至关重要

> 数值

类型 | 大小（byte) | 有符号 | 无符号 | 用途
:---|---|---|---|---
TINYINT	| 1 |	[-128, 127] |	[0, 255] | 整数
SMALLINT | 2 | [-32768, 32767] |	[0, 65535] | 整数
MEDIUMINT	| 3 | [-8 388 608，8 388 607] |	(0，16 777 215)	| 整数
INT或INTEGER |	4 | (-2 147 483 648，2 147 483 647)	| (0，4 294 967 295)	 | 整数
BIGINT |	8 | 	(-9,223,372,036,854,775,808，9 223 372 036 854 775 807)	| (0，18 446 744 073 709 551 615)	| 整数
FLOAT |	4 | 	(-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38)	 | 0，(1.175 494 351 E-38，3.402 823 466 E+38) | 	单精度 浮点数值
DOUBLE |	8 | 	(-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 	0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 	双精度浮点数值
DECIMAL |	对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 |	依赖于M和D的值	| 依赖于M和D的值 | 指定精度小数

> 时间

类型 |	大小( bytes) |	范围 |	用途
:---|:---|:---|:---
DATE |	3	| 1000-01-01/9999-12-31	| YYYY-MM-DD	日期值
TIME |	3	| '-838:59:59'/'838:59:59'	| HH:MM:SS	时间值或持续时间
YEAR |	1 |	1901/2155	YYYY	年份值
DATETIME | 	8 |	1000-01-01 00:00:00/9999-12-31 23:59:59	YYYY-MM-DD HH:MM:SS |	混合日期和时间值
TIMESTAMP |	4 |	1970-01-01 00:00:00/2038 结束时间是第 2147483647 秒，北京时间 2038-1-19 11:14:07，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYYMMDD HHMMSS 混合日期和时间值，时间戳

> 字符

类型 |	大小(byte)	| 用途
:---|---|---
CHAR |	0-255  |	定长字符串
VARCHAR |	0-65535  |	变长字符串
TINYBLOB |	0-255  |	不超过 255 个字符的二进制字符串
TINYTEXT |	0-255  |	短文本字符串
BLOB |	0-65 535  |	二进制形式的长文本数据
TEXT |	0-65 535  |	长文本数据
MEDIUMBLOB |	0-16 777 215  |	二进制形式的中等长度文本数据
MEDIUMTEXT |	0-16 777 215  |	中等长度文本数据
LONGBLOB |	0-4 294 967 295  |	二进制形式的极大文本数据
LONGTEXT |	0-4 294 967 295  |	极大文本数据

### 1.1.1 选择优化的数据类型

下面介绍几个简单的原则：

1.  更小的通常更好

一般情况下，应该尽可能的使用可以正确存储数据的最小的数据类型。小的数据类型占用更小的磁盘，内存和 `CPU` 缓存，并且处理时的CPU周期也更小

1.  简单就好

简单的数据类型的操作通常需要更少的 `CPU周期`。例如，整型比字符操作代价更低

1.  尽量避免 `NULL`

### 1.1. 整数类型

数字包括整数和实数。存储整数可以使用： `TINYINT,SMALLINT MEDIUMINT,INT,BIGINT`. 分别使用 `8，16，24，32，64`位存储空间。

整数类型可选UNSIGNED，表示不允许负值，这可以使正数的上限提高一倍。

有符号和无符号的性能是一样的，因此可以根据实际情况选择合适的类型。

MySQL 可以为整数类型指定宽度，例如 `INT(11),INT(1)`, 对于大多数应用没有意义，因为MySQL不会限制值的合法范围，这只对一些交互工具用来显示使用。 `INT(1)` 和 `INT(11)` 是相同的。

### 1.2. 实数类型

实数是带有小数部分的数字。实数类型既可以存储小数也可以存储比BIGINT大的整数。

`MySQL` 既支持精确类型，也支持不精确类型

`FLOAT` 和 `DOUBLE` 支持标准的浮点运算进行近似计算

`DECIMAL` 支持精确的存储小数，

表需要使用何种数据类型，是需要根据应用来判断的。虽然应用设计的时候需要考虑字段的长度留有一定的冗余，但是不推荐让很多字段都留有大量的冗余，这样即浪费存储也浪费内存。
我们可以使用 PROCEDURE ANALYSE()对当前已有应用的表类型的判断，该函数可以对数据表中的列的数据类型提出优化建议，可以根据应用的实际情况酌情考虑是否实施优化。
语法：

SELECT * FROM tbl_name PROCEDURE ANALYSE(); SELECT * FROM tbl_name PROCEDURE ANALYSE(16,256);
输出的每一列信息都会对数据表中的列的数据类型提出优化建议。第二个例子告诉
PROCEDURE ANALYSE()不要为那些包含的值多于 16 个或者 256 字节的 ENUM 类型提出建议。如果没有这样的限制，输出信息可能很长；ENUM 定义通常很难阅读。

在对字段类型进行优化时，可以根据统计信息并结合应用的实际情况对其进行优化。

## 1.2 拆分表，提供访问效率


这里我们所说的拆分，主要是针对 Myisam 类型的表，拆分的方法可以分成两种情况：
1.纵向拆分：
纵向拆分是只按照应用访问的频度，将表中经常访问的字段和不经常访问的字段拆分成两个表，经常访问的字段尽量是定长的，这样可以有效的提高表的查询和更新的效率。
2.横向拆分：
横向拆分是指按照应用的情况，有目的的将数据横向拆分成几个表或者通过分区分到多个分区中，这样可以有效的避免 Myisam 表的读取和更新导致的锁问题。

## 1.3 逆范式


数据库德规范化设计强调数据的独立性，数据应该尽可能少地冗余，因为存在过多的冗余数据，这就意味着要占用了更多的物理空间，同时也对数据的维护和一致性检查带来了问题。
但是对于查询操作很多的应用，一次查询可能需要访问多表进行，如果通过冗余纪录在相同表中，更新的代价增加不多，但是查询操作效率可以有明显提高，这种情况就可以考虑通过冗余数据来提高效率。

## 1.4 使用冗余统计表

使用create temporary table 语法，它是基于session 的表，表的数据保存在内存里面，当session 断掉后，表自然消除。
对于大表的统计分析，如果统计的数据量不大，利用insert。。。 select 将数据移到临时表中比直接在大表上做统计要效率更高。

## 1.5 选择更合适的表类型

1、如果应用出现比较严重的锁冲突，请考虑是否更改存储引擎到 innodb，行锁机制可以有效的减少锁冲突的出现。
2、如果应用查询操作很多，且对事务完整性要求不严格，则可以考虑使用Myisam

# 2. 定位慢SQL


# 3. 通过 EXPLAIN 分析执行计划

我们可以通过 `explain` 或者 `desc` 来获取 MySQL 的执行计划。


我们可以通过explain 或者desc 获取MySQL 如何执行 SELECT 语句的信息，包括select 语句执行过程表如何连接和连接的次序。
explain 可以知道什么时候必须为表加入索引以得到一个使用索引来寻找记录的更快的SELECT。



- select_type:	select 类型
- table: 输出结果集的表
- type: 表示表的连接类型
  - system: 当表中仅有一行时
  - ref: 使用索引进行表连接时type的值为ref
  - eq_ref: 连接没有使用索引时
  - ALL: 连接没有使用索引时
- possible_keys： 表示查询时,可以使用的索引列. 
- key：	表示使用的索引
- key_len：	索引长度
- rows：	扫描范围
- filtered 
- Extra：	执行情况的说明和描述

当表中仅有一行是type的值为system是最佳的连接类型； 当select操作中使用索引进行表连接时type的值为ref；
当select的表连接没有使用索引时，经常会看到type的值为ALL，表示对该表进行了全表扫描，这时需要考虑通过创建索引来提高表连接的效率。

# 4. 索引

**下列情况下，Mysql 不会使用已有的索引：**

1. 如果 mysql 估计使用索引比全表扫描更慢，则不使用索引。例如：如果key_part1 均匀分布在 1 和 100 之间，下列查询中使用索引就不是很好：
SELECT * FROM table_name where key_part1 > 1 and key_part1 < 90
2. 如果使用 heap 表并且 where 条件中不用＝索引列，其他> 、<、 >=、 <=均不使用索引；
3. 如果不是索引列的第一部分；
4. 如果 like 是以％开始；
5. 对 where 后边条件为字符串的一定要加引号，字符串如果为数字 mysql 会自动转为字符串，但是不使用索引。

# 5. 其他优化措施

1. 使用持久的连接数据库以避免连接开销。
2. 经常检查所有查询确实使用了必要的索引。
3. 避免在频繁更新的表上执行复杂的 SELECT 查询，以避免与锁定表有关的由于读、写冲突发生的问题。
4. 对于没有删除的行操作的 MyISAM 表，插入操作和查询操作可以并行进行，因为没有删除操作的表查询期间不会阻塞插入操作．对于确实需要执行删除操作的表，尽量在空闲时间进行批量删除操作，避免阻塞其他操作。
5. 充分利用列有默认值的事实。只有当插入的值不同于默认值时，才明确地插入值。这减少 MySQL 需要做的语法分析从而提高插入速度。
6. 对经常访问的可以重构的数据使用内存表，可以显著提高访问的效率。
7. 通过复制可以提高某些操作的性能。可以在复制服务器中分布客户的检索以均分负载。为了防止备份期间对应用的影响，可以在复制服务器上执行备份操作。
表的字段尽量不使用自增长变量，在高并发情况下该字段的自增可能对效率有比较大的影响，推荐通过应用来实现字段的自增长。

# 参考

1. 高性能 MySQL
2. https://dev.mysql.com/doc/refman/5.7/en/

# 附录

## show status 相关

| Variable_name                                 | Value                                            |
| --------------------------------------------- | :----------------------------------------------- |
| Aborted_clients                               | 0                                                |
| Aborted_connects                              | 0                                                |
| Binlog_cache_disk_use                         | 0                                                |
| Binlog_cache_use                              | 0                                                |
| Binlog_stmt_cache_disk_use                    | 0                                                |
| Binlog_stmt_cache_use                         | 0                                                |
| Bytes_received                                | 185                                              |
| Bytes_sent                                    | 645                                              |
| Com_admin_commands                            | 0                                                |
| Com_assign_to_keycache                        | 0                                                |
| Com_alter_db                                  | 0                                                |
| Com_alter_db_upgrade                          | 0                                                |
| Com_alter_event                               | 0                                                |
| Com_alter_function                            | 0                                                |
| Com_alter_instance                            | 0                                                |
| Com_alter_procedure                           | 0                                                |
| Com_alter_server                              | 0                                                |
| Com_alter_table                               | 0                                                |
| Com_alter_tablespace                          | 0                                                |
| Com_alter_user                                | 0                                                |
| Com_analyze                                   | 0                                                |
| Com_begin                                     | 0                                                |
| Com_binlog                                    | 0                                                |
| Com_call_procedure                            | 0                                                |
| Com_change_db                                 | 0                                                |
| Com_change_master                             | 0                                                |
| Com_change_repl_filter                        | 0                                                |
| Com_check                                     | 0                                                |
| Com_checksum                                  | 0                                                |
| Com_commit                                    | 0                                                |
| Com_create_db                                 | 0                                                |
| Com_create_event                              | 0                                                |
| Com_create_function                           | 0                                                |
| Com_create_index                              | 0                                                |
| Com_create_procedure                          | 0                                                |
| Com_create_server                             | 0                                                |
| Com_create_table                              | 0                                                |
| Com_create_trigger                            | 0                                                |
| Com_create_udf                                | 0                                                |
| Com_create_user                               | 0                                                |
| Com_create_view                               | 0                                                |
| Com_dealloc_sql                               | 0                                                |
| Com_delete                                    | 0                                                |
| Com_delete_multi                              | 0                                                |
| Com_do                                        | 0                                                |
| Com_drop_db                                   | 0                                                |
| Com_drop_event                                | 0                                                |
| Com_drop_function                             | 0                                                |
| Com_drop_index                                | 0                                                |
| Com_drop_procedure                            | 0                                                |
| Com_drop_server                               | 0                                                |
| Com_drop_table                                | 0                                                |
| Com_drop_trigger                              | 0                                                |
| Com_drop_user                                 | 0                                                |
| Com_drop_view                                 | 0                                                |
| Com_empty_query                               | 0                                                |
| Com_execute_sql                               | 0                                                |
| Com_explain_other                             | 0                                                |
| Com_flush                                     | 0                                                |
| Com_get_diagnostics                           | 0                                                |
| Com_grant                                     | 0                                                |
| Com_ha_close                                  | 0                                                |
| Com_ha_open                                   | 0                                                |
| Com_ha_read                                   | 0                                                |
| Com_help                                      | 0                                                |
| Com_insert                                    | 0                                                |
| Com_insert_select                             | 0                                                |
| Com_install_plugin                            | 0                                                |
| Com_kill                                      | 0                                                |
| Com_load                                      | 0                                                |
| Com_lock_tables                               | 0                                                |
| Com_optimize                                  | 0                                                |
| Com_preload_keys                              | 0                                                |
| Com_prepare_sql                               | 0                                                |
| Com_purge                                     | 0                                                |
| Com_purge_before_date                         | 0                                                |
| Com_release_savepoint                         | 0                                                |
| Com_rename_table                              | 0                                                |
| Com_rename_user                               | 0                                                |
| Com_repair                                    | 0                                                |
| Com_replace                                   | 0                                                |
| Com_replace_select                            | 0                                                |
| Com_reset                                     | 0                                                |
| Com_resignal                                  | 0                                                |
| Com_revoke                                    | 0                                                |
| Com_revoke_all                                | 0                                                |
| Com_rollback                                  | 0                                                |
| Com_rollback_to_savepoint                     | 0                                                |
| Com_savepoint                                 | 0                                                |
| Com_select                                    | 2                                                |
| Com_set_option                                | 0                                                |
| Com_signal                                    | 0                                                |
| Com_show_binlog_events                        | 0                                                |
| Com_show_binlogs                              | 0                                                |
| Com_show_charsets                             | 0                                                |
| Com_show_collations                           | 0                                                |
| Com_show_create_db                            | 0                                                |
| Com_show_create_event                         | 0                                                |
| Com_show_create_func                          | 0                                                |
| Com_show_create_proc                          | 0                                                |
| Com_show_create_table                         | 0                                                |
| Com_show_create_trigger                       | 0                                                |
| Com_show_databases                            | 1                                                |
| Com_show_engine_logs                          | 0                                                |
| Com_show_engine_mutex                         | 0                                                |
| Com_show_engine_status                        | 0                                                |
| Com_show_events                               | 0                                                |
| Com_show_errors                               | 0                                                |
| Com_show_fields                               | 0                                                |
| Com_show_function_code                        | 0                                                |
| Com_show_function_status                      | 0                                                |
| Com_show_grants                               | 0                                                |
| Com_show_keys                                 | 0                                                |
| Com_show_master_status                        | 0                                                |
| Com_show_open_tables                          | 0                                                |
| Com_show_plugins                              | 0                                                |
| Com_show_privileges                           | 0                                                |
| Com_show_procedure_code                       | 0                                                |
| Com_show_procedure_status                     | 0                                                |
| Com_show_processlist                          | 0                                                |
| Com_show_profile                              | 0                                                |
| Com_show_profiles                             | 0                                                |
| Com_show_relaylog_events                      | 0                                                |
| Com_show_slave_hosts                          | 0                                                |
| Com_show_slave_status                         | 0                                                |
| Com_show_status                               | 1                                                |
| Com_show_storage_engines                      | 0                                                |
| Com_show_table_status                         | 0                                                |
| Com_show_tables                               | 0                                                |
| Com_show_triggers                             | 0                                                |
| Com_show_variables                            | 0                                                |
| Com_show_warnings                             | 0                                                |
| Com_show_create_user                          | 0                                                |
| Com_shutdown                                  | 0                                                |
| Com_slave_start                               | 0                                                |
| Com_slave_stop                                | 0                                                |
| Com_group_replication_start                   | 0                                                |
| Com_group_replication_stop                    | 0                                                |
| Com_stmt_execute                              | 0                                                |
| Com_stmt_close                                | 0                                                |
| Com_stmt_fetch                                | 0                                                |
| Com_stmt_prepare                              | 0                                                |
| Com_stmt_reset                                | 0                                                |
| Com_stmt_send_long_data                       | 0                                                |
| Com_truncate                                  | 0                                                |
| Com_uninstall_plugin                          | 0                                                |
| Com_unlock_tables                             | 0                                                |
| Com_update                                    | 0                                                |
| Com_update_multi                              | 0                                                |
| Com_xa_commit                                 | 0                                                |
| Com_xa_end                                    | 0                                                |
| Com_xa_prepare                                | 0                                                |
| Com_xa_recover                                | 0                                                |
| Com_xa_rollback                               | 0                                                |
| Com_xa_start                                  | 0                                                |
| Com_stmt_reprepare                            | 0                                                |
| Compression                                   | OFF                                              |
| Connection_errors_accept                      | 0                                                |
| Connection_errors_internal                    | 0                                                |
| Connection_errors_max_connections             | 0                                                |
| Connection_errors_peer_address                | 0                                                |
| Connection_errors_select                      | 0                                                |
| Connection_errors_tcpwrap                     | 0                                                |
| Connections                                   | 4                                                |
| Created_tmp_disk_tables                       | 0                                                |
| Created_tmp_files                             | 5                                                |
| Created_tmp_tables                            | 1                                                |
| Delayed_errors                                | 0                                                |
| Delayed_insert_threads                        | 0                                                |
| Delayed_writes                                | 0                                                |
| Flush_commands                                | 1                                                |
| Handler_commit                                | 0                                                |
| Handler_delete                                | 0                                                |
| Handler_discover                              | 0                                                |
| Handler_external_lock                         | 0                                                |
| Handler_mrr_init                              | 0                                                |
| Handler_prepare                               | 0                                                |
| Handler_read_first                            | 0                                                |
| Handler_read_key                              | 0                                                |
| Handler_read_last                             | 0                                                |
| Handler_read_next                             | 0                                                |
| Handler_read_prev                             | 0                                                |
| Handler_read_rnd                              | 0                                                |
| Handler_read_rnd_next                         | 21                                               |
| Handler_rollback                              | 0                                                |
| Handler_savepoint                             | 0                                                |
| Handler_savepoint_rollback                    | 0                                                |
| Handler_update                                | 0                                                |
| Handler_write                                 | 20                                               |
| Innodb_buffer_pool_dump_status                | Dumping of buffer pool not started               |
| Innodb_buffer_pool_load_status                | Buffer pool(s) load completed at 201126 20:19:10 |
| Innodb_buffer_pool_resize_status              |
| Innodb_buffer_pool_pages_data                 | 490                                              |
| Innodb_buffer_pool_bytes_data                 | 8028160                                          |
| Innodb_buffer_pool_pages_dirty                | 0                                                |
| Innodb_buffer_pool_bytes_dirty                | 0                                                |
| Innodb_buffer_pool_pages_flushed              | 37                                               |
| Innodb_buffer_pool_pages_free                 | 7697                                             |
| Innodb_buffer_pool_pages_misc                 | 4                                                |
| Innodb_buffer_pool_pages_total                | 8191                                             |
| Innodb_buffer_pool_read_ahead_rnd             | 0                                                |
| Innodb_buffer_pool_read_ahead                 | 0                                                |
| Innodb_buffer_pool_read_ahead_evicted         | 0                                                |
| Innodb_buffer_pool_read_requests              | 5388                                             |
| Innodb_buffer_pool_reads                      | 456                                              |
| Innodb_buffer_pool_wait_free                  | 0                                                |
| Innodb_buffer_pool_write_requests             | 515                                              |
| Innodb_data_fsyncs                            | 7                                                |
| Innodb_data_pending_fsyncs                    | 0                                                |
| Innodb_data_pending_reads                     | 0                                                |
| Innodb_data_pending_writes                    | 0                                                |
| Innodb_data_read                              | 7541248                                          |
| Innodb_data_reads                             | 526                                              |
| Innodb_data_writes                            | 54                                               |
| Innodb_data_written                           | 641024                                           |
| Innodb_dblwr_pages_written                    | 2                                                |
| Innodb_dblwr_writes                           | 1                                                |
| Innodb_log_waits                              | 0                                                |
| Innodb_log_write_requests                     | 0                                                |
| Innodb_log_writes                             | 2                                                |
| Innodb_os_log_fsyncs                          | 4                                                |
| Innodb_os_log_pending_fsyncs                  | 0                                                |
| Innodb_os_log_pending_writes                  | 0                                                |
| Innodb_os_log_written                         | 1024                                             |
| Innodb_page_size                              | 16384                                            |
| Innodb_pages_created                          | 35                                               |
| Innodb_pages_read                             | 455                                              |
| Innodb_pages_written                          | 37                                               |
| Innodb_row_lock_current_waits                 | 0                                                |
| Innodb_row_lock_time                          | 0                                                |
| Innodb_row_lock_time_avg                      | 0                                                |
| Innodb_row_lock_time_max                      | 0                                                |
| Innodb_row_lock_waits                         | 0                                                |
| Innodb_rows_deleted                           | 0                                                |
| Innodb_rows_inserted                          | 0                                                |
| Innodb_rows_read                              | 8                                                |
| Innodb_rows_updated                           | 0                                                |
| Innodb_num_open_files                         | 62                                               |
| Innodb_truncated_status_writes                | 0                                                |
| Innodb_available_undo_logs                    | 128                                              |
| Key_blocks_not_flushed                        | 0                                                |
| Key_blocks_unused                             | 6695                                             |
| Key_blocks_used                               | 3                                                |
| Key_read_requests                             | 6                                                |
| Key_reads                                     | 3                                                |
| Key_write_requests                            | 0                                                |
| Key_writes                                    | 0                                                |
| Last_query_cost                               | 0                                                |
| Last_query_partial_plans                      | 0                                                |
| Locked_connects                               | 0                                                |
| Max_execution_time_exceeded                   | 0                                                |
| Max_execution_time_set                        | 0                                                |
| Max_execution_time_set_failed                 | 0                                                |
| Max_used_connections                          | 1                                                |
| Max_used_connections_time                     | 44161.9190625                                    |
| Not_flushed_delayed_rows                      | 0                                                |
| Ongoing_anonymous_transaction_count           | 0                                                |
| Open_files                                    | 14                                               |
| Open_streams                                  | 0                                                |
| Open_table_definitions                        | 791                                              |
| Open_tables                                   | 99                                               |
| Opened_files                                  | 939                                              |
| Opened_table_definitions                      | 0                                                |
| Opened_tables                                 | 0                                                |
| Performance_schema_accounts_lost              | 0                                                |
| Performance_schema_cond_classes_lost          | 0                                                |
| Performance_schema_cond_instances_lost        | 0                                                |
| Performance_schema_digest_lost                | 0                                                |
| Performance_schema_file_classes_lost          | 0                                                |
| Performance_schema_file_handles_lost          | 0                                                |
| Performance_schema_file_instances_lost        | 0                                                |
| Performance_schema_hosts_lost                 | 0                                                |
| Performance_schema_index_stat_lost            | 0                                                |
| Performance_schema_locker_lost                | 0                                                |
| Performance_schema_memory_classes_lost        | 0                                                |
| Performance_schema_metadata_lock_lost         | 0                                                |
| Performance_schema_mutex_classes_lost         | 0                                                |
| Performance_schema_mutex_instances_lost       | 0                                                |
| Performance_schema_nested_statement_lost      | 0                                                |
| Performance_schema_prepared_statements_lost   | 0                                                |
| Performance_schema_program_lost               | 0                                                |
| Performance_schema_rwlock_classes_lost        | 0                                                |
| Performance_schema_rwlock_instances_lost      | 0                                                |
| Performance_schema_session_connect_attrs_lost | 0                                                |
| Performance_schema_socket_classes_lost        | 0                                                |
| Performance_schema_socket_instances_lost      | 0                                                |
| Performance_schema_stage_classes_lost         | 0                                                |
| Performance_schema_statement_classes_lost     | 0                                                |
| Performance_schema_table_handles_lost         | 0                                                |
| Performance_schema_table_instances_lost       | 0                                                |
| Performance_schema_table_lock_stat_lost       | 0                                                |
| Performance_schema_thread_classes_lost        | 0                                                |
| Performance_schema_thread_instances_lost      | 0                                                |
| Performance_schema_users_lost                 | 0                                                |
| Prepared_stmt_count                           | 0                                                |
| Qcache_free_blocks                            | 1                                                |
| Qcache_free_memory                            | 1031832                                          |
| Qcache_hits                                   | 0                                                |
| Qcache_inserts                                | 0                                                |
| Qcache_lowmem_prunes                          | 0                                                |
| Qcache_not_cached                             | 2                                                |
| Qcache_queries_in_cache                       | 0                                                |
| Qcache_total_blocks                           | 1                                                |
| Queries                                       | 6                                                |
| Questions                                     | 4                                                |
| Select_full_join                              | 0                                                |
| Select_full_range_join                        | 0                                                |
| Select_range                                  | 0                                                |
| Select_range_check                            | 0                                                |
| Select_scan                                   | 1                                                |
| Slave_open_temp_tables                        | 0                                                |
| Slow_launch_threads                           | 0                                                |
| Slow_queries                                  | 0                                                |
| Sort_merge_passes                             | 0                                                |
| Sort_range                                    | 0                                                |
| Sort_rows                                     | 0                                                |
| Sort_scan                                     | 0                                                |
| Ssl_accept_renegotiates                       | 0                                                |
| Ssl_accepts                                   | 0                                                |
| Ssl_callback_cache_hits                       | 0                                                |
| Ssl_cipher                                    |
| Ssl_cipher_list                               |
| Ssl_client_connects                           | 0                                                |
| Ssl_connect_renegotiates                      | 0                                                |
| Ssl_ctx_verify_depth                          | 0                                                |
| Ssl_ctx_verify_mode                           | 0                                                |
| Ssl_default_timeout                           | 0                                                |
| Ssl_finished_accepts                          | 0                                                |
| Ssl_finished_connects                         | 0                                                |
| Ssl_server_not_after                          |
| Ssl_server_not_before                         |
| Ssl_session_cache_hits                        | 0                                                |
| Ssl_session_cache_misses                      | 0                                                |
| Ssl_session_cache_mode                        | NONE                                             |
| Ssl_session_cache_overflows                   | 0                                                |
| Ssl_session_cache_size                        | 0                                                |
| Ssl_session_cache_timeouts                    | 0                                                |
| Ssl_sessions_reused                           | 0                                                |
| Ssl_used_session_cache_entries                | 0                                                |
| Ssl_verify_depth                              | 0                                                |
| Ssl_verify_mode                               | 0                                                |
| Ssl_version                                   |
| Table_locks_immediate                         | 99                                               |
| Table_locks_waited                            | 0                                                |
| Table_open_cache_hits                         | 0                                                |
| Table_open_cache_misses                       | 0                                                |
| Table_open_cache_overflows                    | 0                                                |
| Tc_log_max_pages_used                         | 0                                                |
| Tc_log_page_size                              | 0                                                |
| Tc_log_page_waits                             | 0                                                |
| Threads_cached                                | 0                                                |
| Threads_connected                             | 1                                                |
| Threads_created                               | 1                                                |
| Threads_running                               | 1                                                |
| Uptime                                        | 6268                                             |
| Uptime_since_flush_status                     | 6268                                             |

