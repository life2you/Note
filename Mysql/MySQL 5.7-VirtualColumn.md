---
title: 记一次生产实践MySQL5.7VirtualColumn
tags: [mysql] 
categories:
	- mysql
type : "tags"
---

由于是生产部分内容进行改动及时间将近一年我只记得当时当时采用使用VirtualColumn是因为表数据量较大.

在接到一个新功能主要的逻辑是对两个表的数据进行比对,以下是我遇到的问题及优化结果:

#### <font color= red >在做两表join时,有两个字段作为on字句的条件有点特殊</font>

**1.时间字段 :**

**A 表中的数据是 2018-12-20 00:00:00 这种**

**B 表中的数据是 2018-12-23 12:01:01 这种**

**2 字符串字段**

**A 表中的数据是 '0123456789' 这种**

**B 表中的数据是   '123456789' 这种**

**以上的字段都是存在索引的**

**<font color=red>以下explain结果是将生产数据导出在本地做的测试结果</font>**

**SQL:**

```
on DATE_FORMAT(A.date_field,'%Y-%m-%d') = DATE_FORMAT(B.date_field,'%Y-%m-%d')

and A.string = concat("0",B.string)

或

on date(A.date_field) = date(B.date_field)

and A.string = concat("0",B.string)

```

<font color=red>以上的测试方法性能很差,使用explain 出的 query_cost 为166660左右</font>



**最后采用 VirtualColumns 来优化:**

**表A中新增加 VirtualColumns (virtual_date_field)   对 A.date_field 做date(A.date_field)** 

**并对virtual_date_field 添加索引**

**表B中新增加 VirtualColumns (virtual_date_field) 对  B.date_field 做date(B.date_field)**

**和 VirtualColumns (virtual_string)   对 B.string 做concat("0",B.string)**

**并对B表中新增的virtual_date_field,virtual_string添加索引**



**SQL:**

```
on A.virtual_date_field = B.virtual_date_field
and A.string = B.virtual_string
```

<font color=red>此时使用explain 出的 query_cost 为7792左右</font>



### VirtualColumn

**在创建VirtualColumn时,代价很小，因为VirtualColumn创建后时保存在表的元数据中,不会将该列的数据持久化到磁盘上.但是在对VirtualColumn做索引添加时的代价跟普通字段添加索引代价是差不多的.**

**在做数据插入,更新时不要对VirtualColumn字段做操作**



#### 查询文档:

[VirtualColumn使用介绍-英文](https://www.percona.com/blog/2016/03/04/virtual-columns-in-mysql-and-mariadb/)

[VirtualColumn使用介绍-中文](http://mingxinglai.com/cn/2015/12/mysql5.7-virtal-column/)