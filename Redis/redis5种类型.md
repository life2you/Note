---
title: redis5种类型
tags: [redis] 
categories:
	- redis
type : "tags"

---

#### String类型

&#8195;&#8195;String是redis中最基本的类型，既可以存储文本，也可以存储数字，也可以存储二进制。

- SET key value [ex seconds]：将key设置成指定的value值，若key一存在，则会更新（无论类型是否是string），`ex seconds`表示设置key的过期时间，单位为秒，若不设置，表示key永不过期。
- MSET key value[ key value ... ] ：一次性设置多个key，相当于多次调用set，该操作是原子的。
- MSETNX key value [key value...]：一次性添加多个key，如果有任何一个key存在，那么这个操作都不会执行，所以，当使用`MSETNX`时，要么全部key添加成功，要么全部不被添加。`NX`表示只有key不存在的时候才会设置key的value值。
- GET key：获取耽搁key对应的value
- MGET key [key ...]：在GET命令的前面增加一个M，表示多个`multi`，使用MGET可以同时获取多个Value值，主要在于减少网络传输的次数
- STRLEN key：返key对应的value值的长度，如果key对应的value不是string类型则会报错。如果返回0时，有可能是空字符串，或者是key不存在。
- INCR key：为key对应的整数value值增加1
- DECR key：为key对应的整数value值减少1
- INCRBY key increment：为key对应的整数增加increment
- DECRBY key decrement：为key对应的整数减少 decrement
- INCRBYFLOAT key increment：为key对应的浮点数增加increment



#### List类型

&#8195;&#8195;Redis的List类型是基于双向链表实现的可以支持正向，反向查找和遍历，一个list列表最多可以包括`2的32次方-1`个元素（可以超过40亿个元素）。

- RPUSH key value [value ...]：右推入，也叫后推入，将多个或一个value值依次推入到列表的尾部，若key不存在，则先创建一个空的list列表。
- LPUSH：LPUSH key value [value ...]：左推入，也叫前推入，跟LPUSH几乎一样。
- LPOP key：左弹出，获取元素并删除，从链表最左边获取一个元素，并删除。
- RPOP key：右弹出，与LPOP功能几乎一样，从队列最右边获取元素并移除。
- LLEN key：获取列表的长度。
- LINDEX key index：获取指定索引上的元素。
- LRANGE key start end： 获取指定范围的所有元素。
- LSET key index value：设置指定索引上的值

> List列表的下标是从0开始的，index为负的时候是从后向前熟，-1表示最后一个元素，当下标超出边界会返回nil。



#### HASH表

&#8195;&#8195;redis中的hash哈希表是一个String类型的field字段和value值之间的映射表，类似于Java中Hashmap。

- HSET key field value：设置字段值，如果之前存在该Field那么则进行覆盖，返回0，反之返回0。
- HGET key field：获取字段值。
- HEXISTS key field：检查指定field字段是否存在，存在则返回1，不存在则返回0
- HDEL key field [field...] 删除key哈希表中的一个或多个指定的field字段。
- HKEYS key：获取key哈希表所有的field字段，类似KeySet
- HVALS key：获取key哈希表所有的field字段value，类似values



#### SET集合

&#8195;&#8195;Set集合也是一个列表，可以自动去掉重复元素，当需要存储一个类表，又不希望有重复的元素，可以使用Set，并且Set拥有一个命令，可以判断某个元素是否存在，而list类型并没有这种功能的命令。

- SADD key member1 [member2 ....] 添加元素，可以向key集合中添加一个或多个成员
- SREM key member1 [member2 ...] 删除元素，从key集合中移除一个或多个成员
- SISMEMBER key member 判断某个元素是否属于key集合的成员。
- SCARD key：获取集合的成员数，
- SMEMBERS key：获取集合中所有元素。



#### ZSET有序集合

&#8195;&#8195;ZSet有序集合和Set集合的使用场景类似，区别是有序集合会根据提供的socre参数来进行排序，当需要一个不重复的且有序的集合列表，那么就可以选择ZSet有序列表。

&#8195;&#8195;ZSet有序集合和Set集合不同的是，有序集合的每个元素，都关联着一个分值（score），这是一个浮点数格式的关联值，Zset会根据score从小到大的顺序来排列集合中的元素。

- ZADD key score1 member1 [scoreN memberN ....]：向集合中添加一个或多个成员
- ZREM key member1 [memberN...] 从有序集合中移除一个或多个成员
- ZSCORE key member：从有序集合key中取得member的分数值
- ZRANK key member：从有序集合Key中，取得member成员的分数值的排名。
- ZINCRBY key socre member ：在key集合中，对指定成员的分数加上score
- ZRANGEBYSCORE key min max [WITHSCORE] [LIMIT] 
- ZCARD key :获取key集合中元素个数
- ZCOUNT key min max ：在key集合中 计算指定区间分数的成员数。
