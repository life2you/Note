---
title: Java8-Stream-Api了解
tags: [java,Stream]
categories:
	- java
type : "tags"
---

​	Stream 作为 Java 8 的一大亮点，它与 java.io 包里的 InputStream 和 OutputStream 是完全不同的概念。Java 8 中的 Stream 是对集合(Collection)对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作(aggregate operation)，或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序.

---

#### 生成流的方式

```java
Stream stream = Stream.of("1","2","3");
```

```java
List<String> numberStrings = Arrays.asList(new String[]{"1", "2", "3"});
Stream stream = Stream.of(numberStrings);
```

```java
Stream<String> stream = Arrays.stream(new String[]{"1", "2", "3"});
```

```java
List<String> numberStrings = Arrays.asList(new String[]{"1", "2", "3"});
Stream<String> stream = numberStrings.stream();
```



---

#### 流的操作方式分为两种:terminal,Intermediate

- **Intermediate** 

主要操作是为了打开流,以及对流数据的过滤或映射,会返回一个新的流,不会对源数据(操作的流)进行任何改变,该类操作是惰性的,并不会即时执行,当执行了terminal类操作时才会真正执行Intermediate类操作.一个流后面可以存在零个或多个Intermediate操作.

- **terminal**

一个流只能有一个terminal操作,当terminal操作执行完,这个流也就被使用完了,流(源数据)无法再被使用.所以terminal必定是最后一个操作.



---

#### terminal和Intermediate的归类

- Intermediate

| 操作名称   | 归类 |
| ---------- | ---- |
| map        |      |
| filter     |      |
| distinct   |      |
| sorted     |      |
| peek       |      |
| limit      |      |
| skip       |      |
| parallel   |      |
| sequential |      |



- terminal

| 操作名称       | 归类 |
| -------------- | ---- |
| forEach        |      |
| forEachOrdered |      |
| toArray        |      |
| reduce         |      |
| collect        |      |
| min            |      |
| max            |      |
| count          |      |

