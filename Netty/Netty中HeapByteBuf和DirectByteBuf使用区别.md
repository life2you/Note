---
title: Netty中HeapByteBuf和DirectByteBuf使用区别
tags: [Netty]
---

> 以下代码使用Netty版本`4.1.65.Final`
#### ByteBuf创建方式
1.池化管理直接内存
```java
    ByteBuf buffer = null;
    buffer = ByteBufAllocator.DEFAULT.buffer();
    buffer = ByteBufAllocator.DEFAULT.directBuffer();
    buffer = PooledByteBufAllocator.DEFAULT.directBuffer();
    buffer = PooledByteBufAllocator.DEFAULT.buffer();
```
2.池化管理堆内存
```java
    ByteBuf buffer = null;
    buffer = ByteBufAllocator.DEFAULT.heapBuffer();
    buffer = PooledByteBufAllocator.DEFAULT.heapBuffer();
```
3. 非池化管理直接内存
```java
    ByteBuf buffer = null;
    buffer = UnpooledByteBufAllocator.DEFAULT.buffer();
    buffer = UnpooledByteBufAllocator.DEFAULT.directBuffer();
```
4. 非池化管理堆内存
```java
    ByteBuf buffer = null;
    buffer = UnpooledByteBufAllocator.DEFAULT.heapBuffer();
```



#### HeapByteBuf（堆内存）

&#8195;&#8195;HeapByteBuf缓存区可以直接通过`array()`获取内部数组，通过调用`hasArray()`判断是否是HeapByteBuf。

- 使用方式:
```java
    ByteBuf buffer = ByteBufAllocator.DEFAULT.heapBuffer();
    buffer.writeBytes("测试HeapByteBuf使用方式".getBytes("UTF-8"));
    if (buffer.hasArray()){
        byte[] array = buffer.array();

        int start = buffer.arrayOffset() + buffer.readerIndex();

        int length = buffer.readableBytes();

        System.out.println(new String(array, start, length, Charset.forName("UTF-8")));
    }
    buffer.release();
```

#### DirectByteBuf（直接内存）
&#8195;&#8195;DirectByteBuf缓存区不能读取内部数组，通过调用`hasArray()`为`false`则为`DirectByteBuf`。

- 使用方式
```java

    buffer = ByteBufAllocator.DEFAULT.buffer();
    buffer.writeBytes("测试DirectByteBuf使用方式".getBytes("UTF-8"));
    if (!buffer.hasArray()){
        int length = buffer.readableBytes();
        byte[] bytes = new byte[length];
        buffer.getBytes(buffer.readerIndex(), bytes);
        System.out.println(new String(bytes));
    }
    buffer.release();

```

> ps: `hasArray()` 返回`false`不一定代表缓存区就是`DirectByteBuf`类型，也有可能是`CompositeByteBuf`缓存区。
