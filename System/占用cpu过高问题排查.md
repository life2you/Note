---
title: java程序占用CPU过高
tags: [java,jvm,liunx]
categories: 
	- java
---

1.使用top命令查看占用CPU过高的进程

```shell
top
```

<img src="/image/liunx_top_cpu_hight.png"/>

2.获取线程信息,并找到占用CPU高的线程,并排序

```shell
ps -mp [pid] -o THREAD,tid,time | sort -rn
```

<img src="/image/liunx_top_cpu_hight_thread_sort.png"/>

图中显示占用CPU的两个线程现在显示并不高说明该线程是在某一刻占用cpu比较高,不过我们依然可以作为示例

3.将需要排查的tid进行16进制转换以方便jstack日志grep

```shell
printf "%x" [tid] 
```

printf "%x" 17744  = 4550

4.打印线程的堆栈信息,根据堆栈的日志来定位问题

```shell
jstack [pid] | grep -A50 [4550]
```

<img src="/image/liunx_top_cpu_hight_jstack_grep.png"/>

ps:在mac中第二步的命令执行不下去,会报出无效指令