---
title: redis基础
abbrlink: 78b568c7
date: 2023-09-25 15:48:26
tags:
---

# redis 简介

Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用
Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储
Redis支持数据的备份，即master-slave模式的数据备份

Redis是单进程的

# redis 持久化方案

## AOF持久化

三种写回策略
always，同步写回，每个子命令执行完，都立即将日志写回磁盘。

everysec，每个命令执行完，只是先把日志写到AOF内存缓冲区，每隔一秒同步到磁盘。

no：只是先把日志写到AOF内存缓冲区，有操作系统去决定何时写入磁盘

数据保证：我们可以设置fsync策略，一般默认是everysec，也可以设置每次写入追加，
所以即使服务死掉了，也最多丢失一秒数据

## RDB持久化

体积更小：相同的数据量rdb数据比aof的小，因为rdb是紧凑型文件。
恢复更快：因为rdb是数据的快照，基本上就是数据的复制，不用重新读取再写入内存。
性能更高:父进程在保存rdb时候只需要fork一个子进程，无需父进程的进行其他io操作，也保证了服务器的性能。

## 如何选择RDB和AOF

如果数据不能丢失，RDB和AOF混用
如果只作为缓存使用，可以承受几分钟的数据丢失的话，可以只使用RDB。
如果只使用AOF，优先使用everysec的写回策略。

# RDB(快照)持久化 配置

    #   900秒（15分钟）内至少1个key值改变（则进行数据库保存--持久化）  
    #   300秒（5分钟）内至少10个key值改变（则进行数据库保存--持久化）  
    #   60秒（1分钟）内至少10000个key值改变（则进行数据库保存--持久化） 
    save 900 1  
    save 300 10  
    save 60 10000 
    
    
    #在默认情况下，如果RDB快照持久化操作被激活（至少一个条件被激活）并且持久化操作失败，Redis则会停止接受更新操作。  
    #这样会让用户了解到数据没有被正确的存储到磁盘上。否则没人会注意到这个问题，可能会造成灾难。  
    #  
    #如果后台存储（持久化）操作进程再次工作，Redis会自动允许更新操作。  
    #  
    #然而，如果你已经恰当的配置了对Redis服务器的监视和备份，你也许想关掉这项功能。  
    #如此一来即使后台保存操作出错,redis也仍然可以继续像平常一样工作。  
    stop-writes-on-bgsave-error yes
    
    #是否压缩数据
    rdbcompression yes
    #没有校验的RDB文件会有一个0校验位，来告诉加载代码跳过校验检查。  
    rdbchecksum yes 
    # 导出数据库的文件名称  
    dbfilename dump.rdb
    
    dir ./
    
#  AOF(Append Only File 日志)  配置
    
    appendonly yes
    appendfilename "appendonly_6379.aof"
    appendfsync everysec
    1、不适用fsync模式，仅仅使用操作系统的写数据策略；（no）
    2、总是启动用fsync；（always） 
    3、每秒同步一次。（everyse）
    
    #在rewrite的时候不进行fsync，避免响应过慢。为了避免丢数据建议设置成no
    #rewrite操作时，appendfsync会被阻塞。如果当前AOF文件很大，
    #那么相应的rewrite时间会变长，appendfsync被阻塞的时间也会更长
    no-appendfsync-on-rewrite no
    
    #AOF文件如果不执行rewrite，文件会不断的增长。
    #可以设置达到一定量就开始执行rewrite，避免磁盘耗尽
    auto-aof-rewrite-percentage 100
    
    #过最后一次rewrite文件大小的百分比，如果设置为0表示不自动aof。
    #如果数据量比较小，设置百分比就会一直执行rewrite，消耗信息，这时候就可以设置最小rewrite的大小。
    auto-aof-rewrite-min-size 64mb
    
    #如果redis异常推出，会导致aof不完整，
    #如果设置为yes，启动时就会加载aof异常文件，如果设置为no，就不会加载，启动失败。
    aof-load-truncated yes
    
    #启动的时候使用rdb文件会加载数据比较快，再执行aof操作，可以减少数据加载时间
    aof-use-rdb-preamble yes
   
   
# 基础配置    

    redis默认是没有使用守护进程进行运行。可以通过daemonize参数进行设置  
    daemonize yes
    loglevel notice
    requirepass  密码
    
    #有限时间内,移除那些TTL值最小的key,ttl最小代表最近要过期
    maxmemory-policy  allkeys-lru
    
    设置在执行淘汰策略时，要考虑的样本数量。
    较大的样本数可以提高淘汰算法的准确性，但也会增加CPU负载。
    默认值为5，您可以根据实际情况进行调整
    maxmemory-samples 10
    
    noeviction(默认策略)：对于写请求不再提供服务，直接返回错误（DEL请求和部分特殊请求除外）
    allkeys-lru：从所有key中使用LRU算法进行淘汰
    volatile-lru：从设置了过期时间的key中使用LRU算法进行淘汰
    allkeys-random：从所有key中随机淘汰数据
    volatile-random：从设置了过期时间的key中随机淘汰
    volatile-ttl：在设置了过期时间的key中，根据key的过期时间进行淘汰，越早过期的越优先被淘汰
    
    #用于设置慢查询的评定时间，也就是说超过此配置项的命令，
    将会被当成慢操作记录在慢查询日志中，
    它执行单位是微秒 (1 秒等于 1000000 微秒)
    slowlog-log-slower-than:100
    
    #用来配置慢查询日志的最大记录数
    slowlog-max-len:100
    
    #表示当 Redis 运行内存超过最大内存时，是否开启 lazy free 机制删除
    lazyfree-lazy-eviction no 
    
    #表示设置了过期时间的键值，当过期之后是否开启 lazy free 机制删除
    lazyfree-lazy-expire no 
    
    #有些指令在处理已存在的键时，会带有一个隐式的 del 键的操作，
    比如rename(修改key的名称) 命令，当目标键已存在，Redis 会先删除目标键，如果这些目标键是一个 big key，
    就会造成阻塞删除的问题，此配置表示在这种场景中是否开启 lazy free 机制删除
    lazyfree-lazy-server-del no 
    
    #针对 slave(从节点) 进行全量数据同步，slave 在加载 master 的 RDB 文件前，
    会运行 flushall 来清理自己的数据，
    它表示此时是否开启 lazy free 机制删除
    slave-lazy-flush no
    
    
    
   


    
    