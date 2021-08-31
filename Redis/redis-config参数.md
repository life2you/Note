---
title: redis配置参数
tags: [redis] 
categories:
	- redis
type : "tags"

---

- port:：端口配置
- bind：主机地址配置
- timeout：连接空闲多长时间要关闭连接，指定为0时时间不限制，默认为0
- dbfilename：指定保存缓存数据库的本地文件名，默认dump.rdb
- dir：指定保存缓存数据的本地文件所存放的目录，默认安装目录
- rdbcompression：指定存储至本地数据库时是否压缩数据，默尔为yes，redis采用LZF压缩，如果为了节省CPU时间可以关闭该选项，但会导致数据库文件变的巨大。
- save： 指定在多长时间类，有多少次Key-Value更新操作，就将数据同步到本地数据库文件，save配置项格式：save <seconds><changes>：seconds表示时间段的长度，changes表示变化的次数。如果在seconds时间段内，变化了changes次，则将redis缓存数据同步到本地文件中。
- requirepass：设置redis连接密码，如果配置连接密码，客户端在连接redis时需要通过AUTH<password> 命令提供密码，默认关闭
- salveof：在主从复制的模式下，设置当前节点为salve节点，需要设置master节点的ip和端口，在redis启动时，会自己从master同步数据，如果已经时slave服务器，则会丢掉旧数据从新同步。
- masterauth：在主从复制的模式下，当master服务节点设置密码保护，salve服务连接master服务需要设置密码
- database：设置缓存数据库的数量，默认数据库数量时16个，id从0-15.默认使用的是0 ， 可以使用select <dbid>命令在指定要使用的数据库
- flushdb 会清楚当前数据中的所有数据，flushall会清楚所有数据库中的数据