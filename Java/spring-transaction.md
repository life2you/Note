---
title: Spring事物级别及事务传播
tags: [Spring,transaction,java] 
categories:
	- java
type : "tags"
---


##### PROPAGATION_REQUIRED:

支持当前事务,如果当前没有事务,就新建一个事务,Spring默认事物

**事务传播** : 

ServiceA.MethodA -> ServiceB.MethodB   :   若ServiceA.MethodA已经存在事务,当进入ServiceB.MethodB时,发现ServiceA.MethodA中已经存在事务,ServiceB.MethodB不会再创建新的事物,若ServiceA.MethodA不存在事务,当进入ServiceB.MethodB时,发现ServiceA.MethodA不存在事务,ServiceB.MethodB将会创建新事务.当ServiceA.MethodA或ServiceB.MethodB任何一点出现异常时,都会进行回滚.

##### PROPAGATION_REQUIRES_NEW

新建事务,如果当前存在事务,把当前事务挂起.新建的事务将和被挂起的事务没有任何关系,是两个独立的事务,外层事务失败回滚之后,不能回滚内层事务执行的结果,内层事务失败抛出异常,外层事务捕获,也可以不处理回滚操作.

**事务传播** : 

ServiceA.MethodA -> ServiceB.MethodB   :   若ServiceA.MethodA已经存在事务(PROPAGATION_REQUIRED)模式,ServiceB.MethodB使用事务(PROPAGATION_REQUIRES_NEW)模式,当进入ServiceB.MethodB时,则将ServiceA.MethodA事务先挂起,ServiceB.MethodB都会创建新的事务,若ServiceB.MethodB事务已经commit, ServiceA.MethodA异常事务回滚也不会导致ServiceB.MethodB事务回滚,若ServiceB.MethodB异常事务回滚,ServiceA.MethodA事务仍可以正常提交除非ServiceB.MethodB抛出的是ServiceA.MethodA需要回滚的异常

##### PROPAGATION_SUPPORTS

支持当前事务，如果当前没有事务，就以非事务方式执行

**事务传播** : 

ServiceA.MethodA -> ServiceB.MethodB   :   若ServiceA.MethodA存在事务,进入ServiceB.MethodB(PROPAGATION_SUPPORTS)事务模式中,那么ServiceB.MethodB会加入ServiceA.MethodA事务中,若ServiceA.MethodA不存在事务,那么ServiceB.MethodB则会以无事务进行.该模式以调用者事务决定.

##### PROPAGATION_NESTED

如果一个活动的事务存在,则运行在一个嵌套的事务中.如果没有活动事务,则按REQUIRED属性执行.它使用了一个单独的事务,这个事务拥有多个可以回滚的保存点.内部事务的回滚不会对外部事务造成影响.它只对DataSourceTransactionManager事务管理器起效.

**事务传播** : 

ServiceA.MethodA -> ServiceB.MethodB   :   若ServiceB.MethodB为PROPAGATION_NESTED事务模式,若ServiceB.MethodB出现异常回滚, 那么ServiceB.MethodB事务将会回滚到执行之前的savePinot,ServiceA.MethodA事务中可以有两种处理方式:

1. 

```java
void methodA() { 

        try { 
            ServiceB.methodB();  // 出现错误,rollback,抛出异常
        } catch (SomeException) { 
						// 执行其他业务, 如
            ServiceC.methodC(); 
        } 

    }
```

2. 

ServiceA.MethodA事务可以回滚或提交,ServiceB.MethodB事务将会回滚到执行之前的savePinot,ServiceA.MethodA事务可以根据自己需求配置来决定时commit还是rollback.

---

[文章摘自](http://www.codeceo.com/article/spring-transactions.html)