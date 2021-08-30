---
title: JVM内存区了解
tags: [jvm] 
categories:
	- jvm
type : "tags"
---

JVM内存区分为两类(heap区、非heap区)

**heap** 

1. Eden Space - 伊甸园
2. Survivor Space - 幸存区
3. Old Gen - 老年代(老年区)

- **EdenSpace**区是当对象被创建时会存放在这个区域中,当该区进行过GC后会将不能被回收的对象存放到空的**SurvivorSpace**区
- **SurvivorSpace**区存放**EdenSpace**区没有被回收的对象,SurvivorSpace有两个,分别为ToSurvivor、FromSurvivor,这两个区域空间大小是相同的,EdenSpace区没有被回收的对象会放进空的Survivor(ToSurvivor),FromSurvivor里不能被回收的对象也会放到ToSurvivor,然后FromSurvivor-ToSurvivor标记互换,始终保持一个空的Survivor

- **OldGen**是存放新生代中经过多次垃圾回收忍让存活的对象或新生代分配不了内存的大对象会直接进入老年代,当老年代放满之后,虚拟机会进行垃圾回收,称为**MajorGC**(由于MajorGC除并发GC外均需对整个堆进行扫描和回收又称为**Full GC**)

​    PS:**EdenSpace**和**SurvivorSpace**都属于新生代,新生代中执行的垃圾回收被称之为**MinorGC**[Young GC]，每一次经过垃圾回收[**MinorGC**]留下的对象age都会加1

**非heap**

1. Code Cache - 代码缓存区
2. Perm Gen - 永久代
3. Jvm Stack - JAVA虚拟机栈
4. Local Method Statck - 本地方法栈

- **CodeCache**是代码缓存区,主要用于存放JIT所编译的代码,代码缓存区在client模式下默认是32M,server模式下默认是48M, 这个值也是可以设置的,通过以下两个参数

```
-XX:ReservedCodeCacheSize=128m
-XX:InitialCodeCacheSize=128
```

​		代码缓存区可能会被充满的,充满时会报出

```
"CompilerThread0" java.lang.OutOfMemoryError: requested 2854248 bytes for Chunk::new. Out of swap space?
```

**JIT编译器是程序在运行期间将java字节码编译成平台相关的二进制代码,因为此编译行为发生在程序运行期间，所以该编译器被称为Just-In-Time编译器**

- **PermGen**全称:Permanent Generation space,表示内存的永久保留区域,被称为永久代。这个区域用于存放Class和meta(元数据)的信息,Class在被Load(加载)的时候被放进这个区域,因为这个区域永远不会被回收,当你的程序load(加载)很多Class的话,可能会出现PermGen space的错误,**该区域默认大小是物理内存的1/64**

- **JvmStack**:JAVA虚拟机栈是描述Java方法运行过程的内存模型,Java虚拟机栈会为每个将要运行的方法创建一个叫做**栈帧**的区域,这些区域保存的信息:

  - 局部变量表(基本数据类型的变量,引用类型的变量,返回的结果的类型变量)
  - 操作数栈
  - 动态链接
  - 方法出口信息

   JAVA虚拟机是由一个个栈帧组成,而每个栈帧里都拥有以上几个信息

- **LocalMethodStatck**本地方法栈和JAVA虚拟机栈大同小异，不过本地方法栈是本地方法运行的内存模型,本地方法在运行的时候也会创建一个**栈帧**,该栈帧保存的信息和**Java虚拟机栈**栈帧保存的信息是相同的,方法执行完后也会出栈并释放空间