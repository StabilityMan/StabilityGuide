# 【剖析 | SOFARPC 框架】之 SOFARPC 连接管理与心跳剖析

> 作者：米麒麟、碧远
> 创作日期：2018-08-29
> 专栏地址：[【剖析 | SOFARPC 框架】](https://www.sofastack.tech/tags/%E5%89%96%E6%9E%90-sofarpc-%E6%A1%86%E6%9E%B6/)

SOFAStack(Scalable Open Financial Architecture)是蚂蚁金服自主研发的金融级分布式中间件，包含了构建金融级云原生架构所需的各个组件，是在金融场景里锤炼出来的最佳实践。

本文为《剖析 | SOFARPC 框架》第三篇，本篇由米麒麟/碧远共同出品。《剖析 | SOFARPC 框架》系列由 SOFA 团队和源码爱好者们出品。

SOFARPC:https://github.com/sofastack/sofa-rpc

## 前言

在 RPC 调用过程中，我们经常会和多个服务端进行远程调用，如果在每次调用的时候，都进行  TCP 连接，会对 RPC 的性能有比较大的影响，因此，实际的场景中，我们经常要对连接进行管理和保持。

SOFARPC 应用心跳包以及断线重连实现,结合系统 tcp-keepalive 机制，来实现对 RPC 连接的管理和保持。

## 连接管理

首先我们将会介绍连接管理相关的一些背景知识。

### 长连接和短连接

短连接，一般是指客户端向服务端发起连接请求。连接建立后，发送数据，接收服务端数据返回，然后触发连接断开，下次再重新重复以上过程。

长连接，则是在建立连接后，发送数据，接收数据，但是不主动断开，并且主动通过心跳等机制来维持这个连接可用，当再次有数据发送请求时，不需要进行建立连接的过程。

一般的，长连接多用于数据发送频繁，点对点的通讯，因为每个 TCP 连接都需要进行握手，这是需要时间的，在一些跨城，或者长距离的情况下，如果每个操作都是先连接，再发送数据的话，那么业务处理速度会降低很多，所以每个操作完后都不断开，再次处理时直接发送数据包即可，节省再次建立连接的过程。

但是，客户端不主动断开，并不是说连接就不会断。因为系统设置原因，网络原因，网络设备防火墙，都可能导致连接断开。因此我们需要实现对长连接的管理。

### TCP 层 keep-alive

#### tcp 的 keep-alive 是什么

tcp-keepalive，顾名思义，它可以尽量让 TCP 连接“活着”，或者让一些对方无响应的 TCP 连接断开，

使用场景主要是：

1. 一些特定环境，比如两个机器之间有防火墙，防火墙能维持的连接有限，可能会自动断开长期无活动的 TCP 连接。

2. 还有就是客户端，断电重启，卡死等等，都会导致 TCP 连接无法释放。

这会导致：

一旦有热数据需要传递，若此时连接已经被中介设备断开，应用程序没有及时感知的话，那么就会导致在一个无效的数据链路层面发送业务数据，结果就是发送失败。

无论是因为客户端意外断电、死机、崩溃、重启，还是中间路由网络无故断开、NAT 超时等，服务器端要做到快速感知失败，减少无效链接操作。

而 tcp-keepalive 机制可以在连接无活动一段时间后，发送一个空 ack，使 TCP 连接不会被防火墙关闭。

#### 默认值

tcp-keepalive，操作系统内核支持，但是不默认开启,应用需要自行开启，开启之后有三个参数会生效，来决定一个 keepalive 的行为。

```makedown
net.ipv4.tcp_keepalive_time = 7200
net.ipv4.tcp_keepalive_probes = 9
net.ipv4.tcp_keepalive_intvl = 75
```

可以通过如下命令查看系统 tcp-keepalive 参数配置。

```bash
sysctl -a | grep keepalive

cat /proc/sys/net/ipv4/tcp_keepalive_time

sysctl net.ipv4.tcp_keepalive_time
```

系统默认值可以通过这个查看。

tcp_keepalive_time，在 TCP 保活打开的情况下，最后一次数据交换到 TCP 发送第一个保活探测包的间隔，即允许的持续空闲时长，或者说每次正常发送心跳的周期，默认值为 7200s（2h）。
tcp_keepalive_probes 在 tcp_keepalive_time 之后，没有接收到对方确认，继续发送保活探测包次数，默认值为 9（次）。
tcp_keepalive_intvl，在 tcp_keepalive_time 之后，没有接收到对方确认，继续发送保活探测包的发送频率，默认值为 75s。

这个不够直观，直接看下面这个图的说明：

![tcp keep-alive 说明](https://cdn.nlark.com/yuque/0/2018/png/156121/1535513238795-57f765f2-908b-4689-b0a7-b6aa3a3599f7.png)

#### 如何使用

应用层，以 Java 的 Netty 为例，服务端和客户端设置即可。

```java
ServerBootstrap b = new ServerBootstrap();
            b.group(bossGroup, workerGroup)
             .channel(NioServerSocketChannel.class)
             .option(ChannelOption.SO_BACKLOG, 100)
             .childOption(ChannelOption.SO_KEEPALIVE, true)
             .handler(new LoggingHandler(LogLevel.INFO))
             .childHandler(new ChannelInitializer<SocketChannel>() {
                 @Override
                 public void initChannel(SocketChannel ch) throws Exception {
                     ch.pipeline().addLast(
                             new EchoServerHandler());
                 }
             });

            // Start the server.
            ChannelFuture f = b.bind(port).sync();

            // Wait until the server socket is closed.
            f.channel().closeFuture().sync();
```

就是这里面的`ChannelOption.SO_KEEPALIVE, true` 对应即可打开.

目前 bolt 中也是默认打开的.

```java
.childOption(ChannelOption.SO_KEEPALIVE,
                Boolean.parseBoolean(System.getProperty(Configs.TCP_SO_KEEPALIVE, "true")));
```

Java 程序只能做到设置 SO_KEEPALIVE 选项，至于 TCP_KEEPCNT，TCP_KEEPIDLE，TCP_KEEPINTVL 等参数配置，只能依赖于 sysctl 配置，系统进行读取。

#### 检查

查看 tcp 连接 tcp_keepalive 状态

```
我们可以用 `netstat -no|grep keepalive` 命令来查看当前哪些 tcp 连接开启了 tcp keepalive.
```

![tcp_keepalive 状态](https://cdn.nlark.com/yuque/0/2018/png/156121/1534919046315-385e6336-a8c7-43af-af63-0aa7a29405c9.png)

### 应用层 keep-alive

应用层 keep-alive 方案，一般叫做心跳包，跟 tcp-keepalive 类似，心跳包就是用来及时监测是否断线的一种机制，通过每间隔一定时间发送心跳数据，来检测对方是否连接，是属于应用程序协议的一部分。

#### 心跳是什么

心跳想要实现的和 tcp keep-alive 是一样的。

由于连接丢失时，TCP 不会立即通知应用程序。比如说，客户端程序断线了，服务端的 TCP 连接不会检测到断线，而是一直处于连接状态。这就带来了很大的麻烦，明明客户端已经断了，服务端还维护着客户端的连接，比如游戏的场景下，用户客户端都关机了，但是连接没有正常关闭，服务端无法知晓,还照常执行着该玩家的游戏逻辑。

听上去和 tcp-alive 类似，那为什么要有应用层心跳?

原因主要是默认的 tcp keep-alive 超时时间太长默认是 7200 秒，也就是 2 个小时。并且是系统级别，一旦更改，影响所有服务器上开启 keep alive 选项的应用行为。另外，socks proxy 会让 tcp keep-alive 失效，
socks 协议只管转发 TCP 层具体的数据包，而不会转发 TCP 协议内的实现细节的包（也做不到）。

所以，一个应用如果使用了 socks 代理，那么 tcp keep-alive 机制就失效了，所以应用要自己有心跳包。

socks proxy 只是一个例子，真实的网络很复杂，可能会有各种原因让 tcp keep-alive 失效。

#### 如何使用

基于 netty 开发的话，还是很简单的。这里不多做介绍，因为后面说到 rpc 中的连接管理的时候，会介绍。

### 应用层心跳还是 Keep-Alive

默认情况下使用 keepalive 周期为2个小时，

#### 系统 keep-alive 优势：

1.TCP协议层面保活探测机制，系统内核完全替上层应用自动给做好了。
2.内核层面计时器相比上层应用，更为高效。
3.上层应用只需要处理数据收发、连接异常通知即可。
4.数据包将更为紧凑。

#### 应用 keep-alive 优势:
关闭 TCP 的 keepalive，完全使用业务层面心跳保活机制。完全应用掌管心跳，灵活和可控，比如每一个连接心跳周期的可根据需要减少或延长。

1.应用的心跳包,具有更大的灵活性，可以自己控制检测的间隔，检测的方式等等。
2.心跳包同时适用于 TCP 和 UDP，在切换 TCP 和 UDP 时，上层的心跳包功能都适用。
3.有些情况下，心跳包可以附带一些其他信息，定时在服务端和客户端之间同步。（比如帧数同步）

所以大多数情况采用业务心跳+TCP keepalive 一起使用，互相作为补充。

## SOFARPC 如何实现

### SOFABolt 基于系统 tcp-keepalive 机制实现

这个比较简单，直接打开 KeepAlive 选项即可。

#### 客户端

RpcConnectionFactory 用于创建 RPC 连接，生成用户触发事件，init() 方法初始化 Bootstrap 通过 option() 方法给每条连接设置 TCP 底层相关的属性，ChannelOption.SO_KEEPALIVE 表示是否开启 TCP 底层心跳机制，默认打开 SO_KEEPALIVE 选项。

```java
/**
 * Rpc connection factory, create rpc connections. And generate user triggered event.
 */
public class RpcConnectionFactory implements ConnectionFactory {
  public void init(final ConnectionEventHandler connectionEventHandler) {
    bootstrap = new Bootstrap();
    bootstrap.group(workerGroup).channel(NioSocketChannel.class)
        ...
        .option(ChannelOption.SO_KEEPALIVE, SystemProperties.tcp_so_keepalive());
    ...
  }
}
```

#### 服务端

RpcServer 服务端启动类 ServerBootstrap 初始化通过 option() 方法给每条连接设置 TCP 底层相关的属性，默认设置 ChannelOption.SO_KEEPALIVE 选项为 true，即表示 RPC 连接开启 TCP 底层心跳机制。

```java
/**
 * Server for Rpc.
 */
public class RpcServer extends RemotingServer {
  protected void doInit() {
    this.bootstrap.group(bossGroup, workerGroup).channel(NioServerSocketChannel.class)
       ...
       .childOption(ChannelOption.SO_KEEPALIVE, SystemProperties.tcp_so_keepalive());
    ...
  }
}
```

### SOFABolt 基于 Netty IdleStateHandler 心跳实现

SOFABolt 基于 Netty 的事件来实现心跳，Netty 的 IdleStateHandler 当连接的空闲时间(读或者写)太长时，将会触发一个 IdleStateEvent 事件，然后 BOLT 通过重写 userEventTrigged 方法来处理该事件。如果连接超过指定时间没有接收或者发送任何的数据即连接的空闲时间太长，IdleStateHandler 使用 IdleStateEvent 事件调用 fireUserEventTriggered() 方法，当检测到 IdleStateEvent 事件执行发送心跳消息等业务逻辑。

![SOFABolt 基于 Netty IdleStateHandler 心跳实现](https://cdn.nlark.com/yuque/0/2018/png/156121/1534923163448-5354810e-a2a6-40c5-ad57-8e1557517752.png)

简而言之，向 Netty 中注册一个处理 Idle 事件的监听器。同时注册的时候，会传入 idle 产生的事件，比如读 IDLE 还是写 IDLE，还是都有，多久没有读写则认为是 IDLE 等。

#### 客户端

```java
final boolean idleSwitch = SystemProperties.tcp_idle_switch();
final int idleTime = SystemProperties.tcp_idle();
final RpcHandler rpcHandler = new RpcHandler(userProcessors);
final HeartbeatHandler heartbeatHandler = new HeartbeatHandler();
bootstrap.handler(new ChannelInitializer<SocketChannel>() {

    protected void initChannel(SocketChannel channel) throws Exception {
        ChannelPipeline pipeline = channel.pipeline();
        ...
        if (idleSwitch) {
            pipeline.addLast("idleStateHandler", new IdleStateHandler(idleTime, idleTime,
                0, TimeUnit.MILLISECONDS));
            pipeline.addLast("heartbeatHandler", heartbeatHandler);
        }
        ...
    }

});
```

SOFABolt 心跳检测客户端默认基于 IdleStateHandler(15000ms, 150000 ms, 0) 即 15 秒没有读或者写操作，注册给了 Netty，之后调用 HeartbeatHandler的userEventTriggered() 方法触发 RpcHeartbeatTrigger 发送心跳消息。RpcHeartbeatTrigger 心跳检测判断成功标准为是否接收到服务端回复成功响应，如果心跳失败次数超过最大心跳次数(默认为 3)则关闭连接。

```java
/**
 * Heart beat triggerd.
 */
@Sharable
public class HeartbeatHandler extends ChannelDuplexHandler {

    @Override
    public void userEventTriggered(final ChannelHandlerContext ctx, Object evt) throws Exception {
        if (evt instanceof IdleStateEvent) {
            ProtocolCode protocolCode = ctx.channel().attr(Connection.PROTOCOL).get();
            Protocol protocol = ProtocolManager.getProtocol(protocolCode);
            protocol.getHeartbeatTrigger().heartbeatTriggered(ctx);
        } else {
            super.userEventTriggered(ctx, evt);
        }
    }
}
```

#### 服务端

SOFABolt 心跳检测服务端默认基于 IdleStateHandler(0,0, 90000 ms) 即 90 秒没有读或者写操作为空闲，调用 ServerIdleHandler的userEventTriggered() 方法触发关闭连接。

SOFABolt 心跳检测由客户端在没有对 TCP 有读或者写操作后触发定时发送心跳消息，服务端接收到提供响应；如果客户端持续没有发送心跳无法满足保活目的则服务端在 90 秒后触发关闭连接操作。正常情况由于默认客户端 15 秒/服务端 90 秒进行心跳检测，因此一般场景服务端不会运行到 90 秒仍旧没有任何读写操作的，并且只有当客户端下线或者抛异常的时候等待 90 秒过后服务端主动关闭与客户端的连接。如果是 tcp-keepalive 需要等到 90 秒之后，在此期间则为读写异常。

```java
this.bootstrap.childHandler(new ChannelInitializer<SocketChannel>() {

    protected void initChannel(SocketChannel channel) throws Exception {
        ...
        if (idleSwitch) {
            pipeline.addLast("idleStateHandler", new IdleStateHandler(0, 0, idleTime,
                TimeUnit.MILLISECONDS));
            pipeline.addLast("serverIdleHandler", serverIdleHandler);
        }
        ...
        createConnection(channel);
    }
    ...
});
```

服务端一旦产生 IDLE，那么说明服务端已经 6 个 15s 没有发送或者接收到数据了。这时候认为客户端已经不可用。直接断开连接。

```java
/**
 * Server Idle handler.
 * 
 * In the server side, the connection will be closed if it is idle for a certain period of time.
 */
@Sharable
public class ServerIdleHandler extends ChannelDuplexHandler {

    private static final Logger logger = BoltLoggerFactory.getLogger("CommonDefault");

    @Override
    public void userEventTriggered(final ChannelHandlerContext ctx, Object evt) throws Exception {
        if (evt instanceof IdleStateEvent) {
            try {
                ctx.close();
            } catch (Exception e) {
                ...
            }
        } else {
            super.userEventTriggered(ctx, evt);
        }
    }
}
```

### SOFARPC 连接管理断开重连实现

通常 RPC 调用过程是不需要断链与重连的。因为每次 RPC 调用过程都校验是否有可用连接，如果没有则新建连接。但有一些场景是需要断链和保持长连接的：

- 自动断连：比如通过 LVS VIP 或者 F5 建立多个连接的场景，因为网络设备的负载均衡机制，有可能某一些连接固定映射到了某几台后端的 RS 上面，此时需要自动断连然后重连，靠建连过程的随机性来实现最终负载均衡。注意开启自动断连的场景通常需要配合重连使用。

- 重连：比如客户端发起建连后由服务端通过双工通信发起请求到客户端，此时如果没有重连机制则无法实现。

连接管理是客户端的逻辑，启动好，连接管理开启异步线程。

![SOFARPC 连接管理断开重连实现](https://cdn.nlark.com/yuque/0/2018/png/156121/1534926707654-5f4c6fc0-cd94-403a-8f99-bc6c164babfd.png)

其中，SOFARPC 连接管理 ConnectionHolder 维护存活的客户端列表 aliveConnections 和失败待重试的客户端列表 retryConnections，RPC 启动守护线程以默认 10 秒的间隔检查存活和失败待重试的客户端列表的可用连接：

1. 检查存活的客户端列表 aliveConnections 是否可用，如果存活列表里连接已经不可用则需要放到待重试列表 retryConnections 里面；

2. 遍历失败待重试的客户端列表 retryConnections，如果连接命中重连周期则进行重连，重连成功放到存活列表 aliveConnections 里面，如果待重试连接多次重连失败则直接丢弃。

核心代码在连接管理器的方法中：

```java
com.alipay.sofa.rpc.client.AllConnectConnectionHolder#doReconnect
```

篇幅有限，我们不贴具体代码，欢迎大家通过源码来学习了解。

## 最后

本文介绍连接管理的策略和 SOFARPC 中连接管理与心跳机制的实现，希望通过这篇文章，大家对此有一个了解，如果对其中有疑问的，也欢迎留言与我们讨论。

## 参考文档

- [TCP-Keepalive-HOWTO](http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/index.html)
- [随手记之TCP Keepalive笔记](http://www.blogjava.net/yongboy/archive/2015/04/14/424413.html)
- [为什么基于TCP的应用需要心跳包](http://hengyunabc.github.io/why-we-need-heartbeat/)
- [Netty心跳简单Demo](http://blog.csdn.net/linuu/article/details/51404264)
- [浅析 Netty 实现心跳机制与断线重连](https://segmentfault.com/a/1190000006931568)



本文转载自：[sofastack.tech 官网专栏](https://www.sofastack.tech/blog/sofa-rpc-connection-management-heartbeat-analysis/)