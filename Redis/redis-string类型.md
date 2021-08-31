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



