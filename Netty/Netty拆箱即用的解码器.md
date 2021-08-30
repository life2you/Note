---
title: Netty拆箱即用的解码器
tags: [Netty]
---

#### Netty拆箱即用的解码器

1. **固定长度数据包解码器（FixedLengthFrameDecoder）**

   适合每个接收到的数据包的长度都是固定的，例如20个字节，在这种场景下只需要把这个解码器加入到流水线中，它会把入站ByteBuf数据包拆分为一个个长度为20的数据包，然后发往下一个channelHandler入站处理器。

   ```java
   public void send(){
           String fixedMsg = "abcdefghijklmnopqrst"; // 长度为20
           ByteBuf buffer = UnpooledByteBufAllocator.DEFAULT.buffer();
           for (int i = 0; i < 10; i++) {
               buffer.writeBytes(fixedMsg.getBytes());
           }
       }
   ```

2. **行分割数据包解码器（LineBasedFrameDecoder）**

   适合每个