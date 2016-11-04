title: protocol buf netty 集成
date: 2016.05.31
categories: protocal #分类
tags: [java,netty,pb]
---
  
 netty是高性能的异步io框架，阿里的dubbo,58系的scf和微博的motan的通信框架，都是选用了netty。
 protoc buf是高性能的序列化方案。 因此，把他们集成在一起，非常地有必要了。
 通过netty，可以非常方便的构建高性能的后台服务。


本demo分别来源于netty和pb的官方手册，目前集成的比较乱，以后应该会重新整理。


## 依赖
maven依赖
    <dependencies>
        <dependency>
            <groupId>io.netty</groupId>
            <artifactId>netty-all</artifactId>
            <version>4.0.23.Final</version>
        </dependency>
        <dependency>
            <groupId>com.google.protobuf</groupId>
            <artifactId>protobuf-java</artifactId>
            <version>2.5.0</version>
        </dependency>
    </dependencies>

## 代码
### pb server
提供初始化功能
```
public class SubReqServer { 
     public void bind(int port) throws Exception{ 
         // 创建两个线程组，一个用于服务器接收客户端连接，一个用于进行socketchannel的网络读写 
         EventLoopGroup bossGroup = new NioEventLoopGroup(); 
         EventLoopGroup workerGroup = new NioEventLoopGroup(); 
  
         try{ 
             ServerBootstrap sb = new ServerBootstrap(); 
             sb.group(bossGroup,workerGroup) 
                     .channel(NioServerSocketChannel.class) 
                     .option(ChannelOption.SO_BACKLOG,1024) 
                     .childHandler(new ChildChannelHandler()); 
  
             ChannelFuture f = sb.bind(port).sync(); 
  
             f.channel().closeFuture().sync(); 
         }finally { 
             bossGroup.shutdownGracefully(); 
             workerGroup.shutdownGracefully(); 
         } 
     } 
  
     private class ChildChannelHandler extends ChannelInitializer<SocketChannel>{ 
  
         protected void initChannel(SocketChannel socketChannel) throws Exception { 
             socketChannel.pipeline().addLast(new ProtobufVarint32FrameDecoder()); 
             socketChannel.pipeline().addLast(new ProtobufDecoder(SubscribeReqProto.SubscribeReq.getDefaultInstance())); 
             socketChannel.pipeline().addLast(new ProtobufVarint32LengthFieldPrepender()); 
             socketChannel.pipeline().addLast(new ProtobufEncoder()); 
             socketChannel.pipeline().addLast(new SubReqServerHandler()); 
  
         } 
     } 
  
     public static void main(String[] args) throws Exception { 
         int port = 9999; 
  
         new SubReqServer().bind(port); 
     } 
 } 
```
### pb server handle
执行解码，业务逻辑功能。
```
@ChannelHandler.Sharable 
 public class SubReqServerHandler extends ChannelInboundHandlerAdapter { 
     private int count; 
     public void channelRead(ChannelHandlerContext ctx, Object msg) { // (2) 
  
         SubscribeReqProto.SubscribeReq req = (SubscribeReqProto.SubscribeReq)msg; 
  
         if(req.getUserName().equalsIgnoreCase("njp")){ 
             System.out.println("service accept client subscribe req : [" + req.toString() + "]"); 
             ctx.writeAndFlush(resp(req.getSubReqID())); 
         } 
  
  
     } 
  
  
     public void channelReadComplete(ChannelHandlerContext ctx) throws Exception { 
         ctx.flush(); 
     } 
  
     public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception { 
         ctx.close(); 
     } 
  
     private SubscribeRespProto.SubscribeResp resp(int id){ 
         return  SubscribeRespProto.SubscribeResp.newBuilder().setSubReqID(id) 
                 .setRespCode(0) 
                 .setDesc("netty book order succeed, 3 days later ,sent to the designated address;") 
                 .build(); 
     } 
 } 

```

### pb client 
提供客户端初始化功能
```
public class SubReqClient { 
  
     public void connect(int port,String host) throws InterruptedException { 
  
         EventLoopGroup group = new NioEventLoopGroup(); 
  
         Bootstrap sb = new Bootstrap(); 
         try { 
             sb.group(group) 
                     .channel(NioSocketChannel.class) 
                     .handler(new ChannelInitializer<SocketChannel>() { 
                         protected void initChannel(SocketChannel socketChannel) throws Exception { 
                             socketChannel.pipeline().addLast(new ProtobufVarint32FrameDecoder()); 
                             socketChannel.pipeline().addLast(new ProtobufDecoder(SubscribeRespProto.SubscribeResp.getDefaultInstance())); 
                             socketChannel.pipeline().addLast(new ProtobufVarint32LengthFieldPrepender()); 
                             socketChannel.pipeline().addLast(new ProtobufEncoder()); 
                             socketChannel.pipeline().addLast(new SubReqClientHandler()); 
                         } 
                     }); 
             ChannelFuture f = sb.connect(host,port).sync(); 
  
             f.channel().closeFuture().sync(); 
         }finally { 
             group.shutdownGracefully(); 
         } 
     } 
  
     public static void main(String[] args){ 
         try { 
             new SubReqClient().connect(9999,"127.0.0.1"); 
         } catch (InterruptedException e) { 
             e.printStackTrace(); 
         } 
     } 
 } 

```
### pb client handle
执行pb编解码，业务逻辑功能。
```
public class SubReqClientHandler extends ChannelInboundHandlerAdapter{ 
     private final String ECHO_REQ = "hi njp, welcome to netty. $_"; 
     private int count; 
  
     public SubReqClientHandler(){ 
  
     } 
  
     @Override 
     public void channelActive(ChannelHandlerContext ctx) { 
        for(int i = 0; i < 10; i++) 
            ctx.writeAndFlush(subReq(i)); 
     } 
  
 /*    public void handlerAdded(ChannelHandlerContext channelHandlerContext) throws Exception { 
         channelHandlerContext.writeAndFlush(firstMessage); 
     }*/ 
  
     public void channelRead(ChannelHandlerContext ctx, Object msg) { // (2) 
  
         System.out.println("this is  : " + ++count  + " times receive server: [ "+ msg + 
                 " ]"); 
  
  
     } 
  
     public void exceptionCaught(ChannelHandlerContext channelHandlerContext, Throwable throwable) throws Exception { 
         channelHandlerContext.close(); 
     } 
  
     private SubscribeReqProto.SubscribeReq subReq(int id){ 
         List<String> address = new ArrayList<String>(); 
         address.add("beijing njp"); 
         address.add("shanghai njp"); 
         return 
                 SubscribeReqProto.SubscribeReq.newBuilder() 
                 .setSubReqID(id) 
                 .addAllAddress(address) 
                 .setProductName("netty in action") 
                 .setUserName("njp") 
                 .build(); 
     } 
 } 

```

## github
 [https://github.com/njkfei/netty-pb-demo](https://github.com/njkfei/netty-pb-demo)
