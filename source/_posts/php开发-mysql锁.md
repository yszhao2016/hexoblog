---
title: php开发-mysql锁
tags: PHP开发
categories:
  - PHP开发
abbrlink: 33571e0f
date: 2024-06-11 22:23:14
---



# 一、数据库中的锁

   数据库锁定机制简单来说，就是数据库为了保证数据的一致性，而使各种共享资源在被并发访问变得有序所设计的一种规则。

## 1.1 表级锁

   使用表级锁定的主要是 MyISAM，MEMORY，CSV 等一些非事务性存储引擎。


## 1.2 行级锁

   使用行级锁定的主要是 InnoDB 存储引擎。


## 行级锁类型

   对于InnoDB 在 RR（MySQL默认隔离级别）而言，对于 update、delete 和 insert 语句，
会自动给涉及数据集加排它锁（X）；

   对于普通 select 语句，innodb 不会加任何锁。
如果想在 select 操作的时候加上 S 锁 或者 X 锁，需要我们手动加锁。

    
### 共享锁（简称 s 锁）
    
  不会阻塞其他事务对同一行的读请求，但会阻塞对同一行的写请求。
  只有当读锁释放后，才会执行其它事物的写操作。

### 排它锁（简称 x 锁）

  会阻塞其他事务对同一行的读和写操作，只有当写锁释放后，才会执行其它事务的读写操作

## Sql示列

```sql
-- 加共享锁（S） lock in share mode
select * from table_name where ... lock in share mode


-- 加排它锁（X)  for update
select * from table_name where ... for update

-- LOCK TABLES 语句用于锁定一个或多个表
LOCK TABLES users READ;
```

InnoDB 中的 行锁 的实现依赖于 索引，一旦某个加锁操作没有使用到索引，那么该锁就会退化为表锁。