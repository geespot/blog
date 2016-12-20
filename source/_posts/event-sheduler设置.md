---
title: event_sheduler设置
date: 2016-12-05 10:34:45
tags:
- 技术
categories: [linux,数据库,开发]
---

##  关于event_scheduler的操作

###  要查看当前是否已开启事件调度器，可执行如下SQL：

```sql
 SHOW VARIABLES LIKE 'event_scheduler';
 SELECT @@event_scheduler;
 SHOW PROCESSLIST;
```   

+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| event_scheduler | OFF   |
+-----------------+-------+

###  启动方法：
```sql
SET GLOBAL event_scheduler = 1;
SET GLOBAL event_scheduler = ON;
```  
或者  my.ini or my.cnf 中的
[mysqld] 添加 
event_scheduler=ON

* 创建事件(CREATE EVENT)
CREATE EVENT [IF NOT EXISTS] event_name
ON SCHEDULE schedule
[ON COMPLETION [NOT] PRESERVE]
[ENABLE | DISABLE]
[COMMENT 'comment']
DO sql_statement;


schedule:
AT TIMESTAMP [+ INTERVAL INTERVAL]
| EVERY INTERVAL [STARTS TIMESTAMP] [ENDS TIMESTAMP]

INTERVAL:
quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE |
            WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |
            DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}


**使用方法** 
### 1)首先来看一个简单的例子来演示每秒插入一条记录到数据表:

```sql
USE test;
CREATE TABLE aaa (timeline TIMESTAMP);
CREATE EVENT e_test_insert
ON SCHEDULE EVERY 1 SECOND 
DO INSERT INTO test.aaa VALUES (CURRENT_TIMESTAMP);
```   
等待3秒钟后，再执行查询成功。

### 2) 5天后清空test表：
```sql
CREATE EVENT e_test
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 5 DAY
DO TRUNCATE TABLE test.aaa;
```   
### 3) 2007年7月20日12点整清空test表：
```sql
CREATE EVENT e_test
ON SCHEDULE AT TIMESTAMP '2007-07-20 12:00:00'
DO TRUNCATE TABLE test.aaa;
```   

### 4) 每天定时清空test表：
```sql
CREATE EVENT e_test
ON SCHEDULE EVERY 1 DAY
DO TRUNCATE TABLE test.aaa;
```   
### 5) 5天后开启每天定时清空test表：
```sql
CREATE EVENT e_test
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP + INTERVAL 5 DAY
DO TRUNCATE TABLE test.aaa;
```   
### 6) 每天定时清空test表，5天后停止执行：
```sql
CREATE EVENT e_test
ON SCHEDULE EVERY 1 DAY
ENDS CURRENT_TIMESTAMP + INTERVAL 5 DAY
DO TRUNCATE TABLE test.aaa;
```   

### 7) 5天后开启每天定时清空test表，一个月后停止执行：
```sql
CREATE EVENT e_test
ON SCHEDULE EVERY 1 DAY
STARTS CURRENT_TIMESTAMP + INTERVAL 5 DAY
ENDS CURRENT_TIMESTAMP + INTERVAL 1 MONTH
DO TRUNCATE TABLE test.aaa;
[ON COMPLETION [NOT] PRESERVE]可以设置这个事件是执行一次还是持久执行，默认为NOT PRESERVE。
```   
### 8) 每天定时清空test表(只执行一次，任务完成后就终止该事件)：
```sql
CREATE EVENT e_test
ON SCHEDULE EVERY 1 DAY
ON COMPLETION NOT PRESERVE
DO TRUNCATE TABLE test.aaa;
```   
[ENABLE | DISABLE]可是设置该事件创建后状态是否开启或关闭，默认为ENABLE。
[COMMENT ‘comment’]可以给该事件加上注释。

修改事件(ALTER EVENT)
ALTER EVENT event_name
[ON SCHEDULE schedule]
[RENAME TO new_event_name]
[ON COMPLETION [NOT] PRESERVE]
[COMMENT 'comment']
[ENABLE | DISABLE]
[DO sql_statement]
1) 临时关闭事件
```sql
ALTER EVENT e_test DISABLE;
```   
2) 开启事件
```sql
ALTER EVENT e_test ENABLE;
```   
3) 将每天清空test表改为5天清空一次：

```sql
ALTER EVENT e_test ON SCHEDULE EVERY 5 DAY;
```   
删除事件(DROP EVENT)
语法很简单，如下所示：

DROP EVENT [IF EXISTS] event_name
例如删除前面创建的e_test事件

DROP EVENT e_test;
当然前提是这个事件存在，否则会产生ERROR 1513 (HY000): Unknown event错误，因此最好加上IF EXISTS

DROP EVENT IF EXISTS e_test;

