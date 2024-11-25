---
title: redis基本数据结构
abbrlink: 660a1c80
date: 2023-09-26 18:47:44
tags:
 - redis
---
# 简介
    
    Redis五大数据类型：String、Hash、List、Set、Sorted set
 

# 字符串

String类型的最大能存储512M 应用场景 【缓存】

    1.SET key value [EX seconds] [PX milliseconds] [NX|XX]
    
    EX和PX参数可选，用于设置键的过期时间，单位分别为秒和毫秒
    
    NX表示只在键不存在时创建，XX表示只在键已存在时执行操作。
    
    将键 "abc" 设置为值 "123" 并设置过期时间为 12 秒
    
        set abc 123 ex 12
   
    2. MSET key1 value1 [key2 value2 ...]  设置多条
         mset a 1 b 2 
           
    3.EXPIRE key seconds    设置过期时间
   
    4.SETEX key seconds value 可用于一次设置带有过期时间的键值对

    select命令切换数据库，分库的作用主要用来业务分片：
    dbsize查看当前数据库的key的数量
    flushdb清除当前库所有key(当前库)：
    

# Hash(哈希表)

    hget：通过key值，从hash里取对应的value
    
    hset：往hash里，添加key-value
    
    hmset
    hmget：一次性获取多个key的value
    
    hmset user:2 name:kati age 28 sex woman
    
    hmget user:2 name age sex

# List(列表)

    lpush：从列表List的左边插入一个元素。
    
    lpop：从列表List的左边移出一个元素。
    
    rpush：从列表List的右边插入一个元素。
    
    rpop：从列表List的右边移出一个元素。
    
    llen：打印当前列表List中的元素个数。

# Set(集合) 

# Sorted set(有序集合)