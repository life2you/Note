---
title: Redis主从服务搭建[一主多从]
tags: [redis] 
categories:
	- redis
type : "tags"
---

#### 下载安装

使用git克隆redis源码到本地

```shell
cd ~/ && mkdir redis-demo && cd redis-demo
git clone https://github.com/antirez/redis.git
```

克隆完成后,进入redis文件中编译redis源码

```shell
cd redis && make
```

编译完成后在redis/src文件下会有redis-server和redis-cli这两个文件,然后将这两个文件拷贝到redis-demo文件下,然后创建

redis-master和redis-7001,redis-7002,redis-7003文件夹为一主三从服务器做区分.如图

<img src="/image/redis_replication_folder.jpg"/>



#### 修改配置

下载redis配置文件

```shell
wget -c http://download.redis.io/redis-stable/redis.conf
```

如果报

```shell
command not found: wget
```

需要安装wget或者直接点击[配置](http://download.redis.io/redis-stable/redis.conf) 将该页面所有内容，创建redis.conf文件，将内容粘贴进去.

分别拷贝redis.conf文件到redis-master,redis-7001,redis-7002,redis-7003文件夹中

进入redis-master文件中修改redis.conf文件,主要将参数修改为以下即可:

port 6379

daemonize yes

pidfile /var/run/redis.pid

logfile "redis.log"

然后启动redis-master服务

```shell
../redis-server ./redis.conf
```

然后分别进入redis-master,redis-7001,redis-7002,redis-7003文件夹中修改redis.conf文件，修改一下参数

port <port> 修改成文件夹名称后面的数字如：7001

daemonize yes

pidfile /var/run/redis.pid

logfile "redis.log"

replicaof 127.0.0.1 6379

修改完成后分别进入redis-master,redis-7001,redis-7002,redis-7003文件夹启动服务

```shell
../redis-server ./redis.conf
```



#### 测试结果

进入redis-demo文件中

```shell
./redis-cli -c -p 6379
```

进入redis主服务命令端输入

```shell
info replication
```

结果如图

<img src="/image/redis_master_info_replication.jpg"/>

图中参数解释:

role:master 表明我们这是一个主服务

slave0,slave1,slave2,slave3:表示关联这个主服务的4个从服务

ip:表示从服务的地址

port:表示该台从服务的端口

state:online 表示该台服务的运行状态

offset:表示从服务拷贝主服务的复制偏移量，其中7003，7004这两台的服务还没有同步所有数据



可以先测试数据会不会同步过去

```shell
set master success
然后
get master
可以看到数据
```

<img src="/image/redis_master_data_test.jpg"/>

然后我们到几个从库里面看下

```shell
./redis-cli -c -p 7001
get master
```

<img src="/image/redis_slave_data_test.jpg"/>

从图中可以看到我们数据同步到从库中来了，其他服务器也是同样，但是我们现在如果在从服务中set数据会被拒绝掉

<img src="/image/redis_slave_write_fail.jpg"/>

告诉你不能向只读文本写数据，这里告诉大家原因是我们从服务redis.confz中的一项配置

replica-read-only yes

这个参数表明这个服务是一个允许读取,你可以修改为no,修改后可以在该服务中写入数据但是不会同步到主服务中，大家可以自己测试下，但是一般不建议设置可写，主从设计就是从服务数据由主服务同步过来.

本次redis主从服务搭建就先到这里，大家可以自己去琢磨下。