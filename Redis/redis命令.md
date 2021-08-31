---
title: redis命令
tags: [redis] 
categories:
	- redis
type : "tags"

---

- set：根据key设置value

- get：根据key获取value，key不存在时，会返回空结果。

- keys：查找所有符合给定模式（pattern）的key，模式支持通配符：
>	?:匹配一个字符
>	*:匹配任何个自负
>	[-]:匹配区间内的任一字符，如a[b-d]可以匹配“ab”,"ac","ad"
>	\:转义符号。使用\?可以匹配“？”

- exists：判断一个key是否存在，如果key存在，则返回1，否则0
- expire：为指定的key设置过期时间，以秒为单位
- ttl：返回指定key的剩余生存时间（ttl，time to live），以秒为单位。如果没有指定时间，默认为-1，表示永久存在
- type：返回key所存储的value类型
- del：删除指定的key对应的value
- ping：检查连接，成功返回pong