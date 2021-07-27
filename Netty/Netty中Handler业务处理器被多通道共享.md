---
title: Netty中Handler业务处理器被多通道共享
tags: [Netty]
---

&#8195;&#8195;Netty中很多应用场景需要Handler业务处理器实例能共享，如果在Handler业务处理器中没有特定的通道强相关的数据或者状态，建议设计成共享模式。

&#8195;&#8195;将Handler设置为共享模式在只需要加上`@ChannelHandler.Sharable`注解即可，Netty默认是不允许一个Handler业务处理被多个通道共享使用的，在没有添加`@ChannelHandler.Sharable`注解时，试图把Handler添加到多个ChannelPipeline通道流水线时，Netty将会抛出异常。


- 示例
```java
    import io.netty.channel.ChannelHandler;
    @ChannelHandler.Sharable
    public class NettyDiscardHandler

```

&#8195;&#8195;同一个通道上的所有业务处理器，只能被同一个线程处理。非`@ChannelHandler.Sharable`共享类型的业务处理器，在线程的层面是安全的，不需要进行线程的同步控制。**而不同的通道，可能绑定到多个不同的EventLoop反应器线程。因此加上`@ChannelHandler.Sharable`注解后的业务处理器的实例，可能被多个线程并发执行。这样就会导致共享业务处理器不是线程层面安全的，所以如果需要操作的数据不仅仅是局部变量，则需要进行线程的同步控制来保证操作是线程层面安全的**。

&#8195;&#8195;如何判断一个Handlder处理器是否是共享的，Netty提供了`isSharable()`方法，如果是共享的则返回`true`， 反之`false`。
