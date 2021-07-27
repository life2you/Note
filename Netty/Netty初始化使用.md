---
title: Netty初始化使用
tags: [Netty]
---

#### Netty初始化服务端使用


- 服务端服务初始化

```java

    private ServerBootstrap sb = new ServerBootstrap();
    public void run() throws InterruptedException {

        // 创建 reactor线程组
        NioEventLoopGroup bossLoopGroup = new NioEventLoopGroup();
        NioEventLoopGroup workerLoopGroup = new NioEventLoopGroup();
        try {
            // 设置reactor线程组
            sb.group(bossLoopGroup,workerLoopGroup);
            // 设置Nio类型的channel
            sb.channel(NioServerSocketChannel.class);
            // 设置监听端口
            sb.localAddress(port);
            // 设置通道的参数
            sb.option(ChannelOption.SO_KEEPALIVE, true);
            sb.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
            sb.childOption(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);
            // 装配子通道流水线
            sb.childHandler(new ChannelInitializer<SocketChannel>() {
                // 有连接时会创建一个SocketChannel
                @Override
                protected void initChannel(SocketChannel ch) throws Exception {
                    // pipeline 管理子通道channel中的handler
                    // 向子通道channel添加handler
                    ch.pipeline().addLast(new NettyDiscardHandler());
                }
            });
            // 开始绑定server
            // 通过调用sync同步方法阻塞知道绑定成功
            ChannelFuture channelFuture = sb.bind().sync();
            System.out.println("服务启动成功，监听端口："+channelFuture.channel().localAddress());
            //  等待通道关闭的异步任务结束
            // 服务监听通道会一直等待通道关闭的异步任务结束
            ChannelFuture closeFuture = channelFuture.channel().closeFuture();
            closeFuture.sync();
        }finally {
            //   关掉Reactor线程组
            workerLoopGroup.shutdownGracefully();
            bossLoopGroup.shutdownGracefully();
        }

```

- 客户端初始化

```java

    Bootstrap bootstrap = new Bootstrap();

    private void run() throws UnsupportedEncodingException, InterruptedException {
        
        NioEventLoopGroup workGroup = new NioEventLoopGroup();

        bootstrap.group(workGroup);

        bootstrap.channel(NioSocketChannel.class);

        bootstrap.remoteAddress("127.0.0.1", 7999);

        bootstrap.option(ChannelOption.ALLOCATOR, PooledByteBufAllocator.DEFAULT);

        bootstrap.handler(new ChannelInitializer<SocketChannel>() {
            @Override
            protected void initChannel(SocketChannel ch) throws Exception {
                ch.pipeline().addLast(new EchoClientHandler());
            }
        });

        ChannelFuture future = bootstrap.connect();
        future.addListener((ChannelFuture cf) ->{
            if (cf.isSuccess()){
                System.out.println("客户端链接成功");
            }else {
                System.out.println("客户端链接失败");
            }
        });
        future.sync();
        Channel channel = future.channel();

        Scanner scanner = new Scanner(System.in);
        System.out.print("请输入发送内容:");
        while (scanner.hasNext()){
            String next = scanner.next();
            String date = new SimpleDateFormat("yyyy-MM-dd").format(new Date());
            byte[] bytes = (date +":"+ next).getBytes("UTF-8");
            ByteBuf buffer = channel.alloc().buffer();
            buffer.writeBytes(bytes);
            channel.writeAndFlush(buffer);
            System.out.println("请输入发送内容:");
        }
    }

```
