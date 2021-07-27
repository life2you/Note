---
title: Netty中CompositeByteBuf分包发送
tags: [Netty]
---

&#8195;&#8195;很多通信场景下需要多个`Buffer`来组成一个消息，如Http协议传输消息时总是由`Header`（消息头）和`Body`（消息体）组成，如果传输内容很长，那么就要分成多个消息包进行发送,消息中的`Header`就要重复使用，不能每次发送都重新创建`Header`了。

- 使用CompositeByteBuf复用Header消息头

```java
    
    CompositeByteBuf cb = ByteBufAllocator.DEFAULT.compositeBuffer();
    ByteBuf headBuffer = Unpooled.copiedBuffer("我是Head头", Charset.defaultCharset());
    ByteBuf bodyBuffer1 = Unpooled.copiedBuffer("我是Body前面", Charset.defaultCharset());
    cb.addComponents(headBuffer,bodyBuffer1);
    sendMsg(cb);
    // 防止 headBuffer 被 refCnt 被 release  为 0
    headBuffer.retain();
    cb.release();

    cb = ByteBufAllocator.DEFAULT.compositeBuffer();
    ByteBuf bodyBuffer2 = Unpooled.copiedBuffer("我是Body后面", Charset.defaultCharset());
    cb.addComponents(headBuffer, bodyBuffer2);
    sendMsg(cb);
    cb.release();
    
    
    
    public static void sendMsg(CompositeByteBuf cbb){

        for (ByteBuf b : cbb){
            int length = b.readableBytes();
            byte[] bytes = new byte[length];
            b.getBytes(b.readerIndex(), bytes);
            System.out.println(new String(bytes));
        }

    }


```
