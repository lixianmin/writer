-----

#### 0x01 概述



##### 01 特性列表

| 特性                                                         | 支持                |      |
| ------------------------------------------------------------ | ------------------- | ---- |
| 协议                                                         | ws, tcp             |      |
| 序列化                                                       | json, protobuf      |      |
| 心跳保活                                                     | √                   |      |
| [unity支持](https://github.com/topfreegames/libpitaya/tree/master/unity/PitayaExample) | 需确认使用unity或js |      |
|                                                              |                     |      |
| RPC                                                          | nats, grpc          |      |
| 服务发现                                                     | etcd                |      |
| cluster                                                      | √                   |      |



##### 02 前后端交互



| 前端          | 后端                                                         |
| ------------- | ------------------------------------------------------------ |
| 前端request   | 1. starx.request("room.join", {}, join)<br />2. func (r \*Room) Join(ctx context.Context, msg []byte)(*JoinResponse, error)<br/>3. 对request请求，服务器是有返回值 |
| 前端notify    | 1. starx.notify("room.message", {name: xxx, content: xxx})<br />2. func (r *Room) Message(cox context.Context, msg *UserMessage)<br />3. 对notify请求，服务器没有返回值，在服务器代码中这**两种请求的handler方法签名不同** |
|               |                                                              |
| 后端Push      | 1. starx.on("onMembers", onMembers);<br />2. session.Push("onMembers", &AllMembers{}) |
| 后端Broadcast | 1. starx.on("onNewUser", onNewUser);<br />2. pitaya.GroupBroadcast(ctx, "chat", "room", "onNewUser", &NewUser{}) |



---

#### 0x02 类型描述



##### 01 概述

概念描述：

| 名字         | 描述                               |
| ------------ | ---------------------------------- |
| context      | 会话上下文                         |
| frontendType |                                    |
| groupName    | 通过分组名操作group                |
| route        | push()到前端时，在前端调用的方法名 |
|              |                                    |
|              |                                    |



类型描述：

| 名字              | 描述                   |
| ----------------- | ---------------------- |
| Group             | 玩家分组，用于广播消息 |
| Handler/Component | 协议处理机制           |
| Session           | 客户端会话             |
|                   |                        |



##### 02 Acceptor

1. 支持NewTCPAcceptor()和NewWSAcceptor()
2. `acceptor.ListenAndServe()`方法是需要监听的，因此它是阻塞方法，因此它需要单独一个协程运行
3. `GetConnChan() chan PlayerConn`，当有新用户连接到服务器的时候，返回一个PlayerConn对象
4. PlayerConn是一个加强版的net.Conn，拥有GetNextMessage()的能力



##### 03 [Group](https://pitaya.readthedocs.io/en/latest/features.html#groups)

1. group：玩家分组，用于广播消息

```go
// gsi是一个group service，它是一个group的集合，而不是group
gsi := groups.NewMemoryGroupService(config.NewConfig(conf))
pitaya.InitGroups(gsi)

// group是通过name进行访问和使用的
err := pitaya.GroupCreate(context.Background(), "room")
pitaya.GroupBroadcast(ctx, "chat", "room", "onNewUser", &NewUser{...})
pitaya.GroupAddMember(ctx, "room", s.UID()) 

```



##### 04 [Handler](https://pitaya.readthedocs.io/en/latest/API.html#handlers)

1. handler：协议处理机制，由util.Pcall()通过反射调用
2. handler分两种：一种是request handler，带返回值；一种是notify handler，无返回值
3. 在pitaya中， component跟handler是一体两面的，不分家。
4. 虽然component拥有像MonoBehaviour类似的Init(), AfterInit()顺序控制机制，但本质上component是一种分类机制，只是handler的载体，跟ECS中的component是两回事。
5. component通过pitaya.Register()注册到系统中，在pitaya.Start()时刻统一初始化。这意味着handler是在游戏启动时就注册好的，在进程运行过程中不会变化。这也反向证明了handler就是协议处理机制。
6. 注意前端调用时使用的**路由是groupName (room)+route (message) 的形式**。

```go
// request handler，有返回值，参数为 (context.Context, *MyStruct或[]byte
// index.html中调用：starx.request("room.join", {}, join);
func (r *Room) Join(ctx context.Context, msg []byte) (*JoinResponse, error) 

// notify handler，无返回值，参数为(context.Context, *MyStruct或[]byte)
// index.html中调用：starx.notify('room.message', {name: this.nickname, content: this.inputMessage});
func (r *Room) Message(ctx context.Context, msg *UserMessage)


// 注册handler，这样客户端就可以使用<servertype>.room.join加入
pitaya.Register(
    &Room{}, // struct to register as handler
    component.WithName("room"), // name of the handler, used by the clients
    component.WithNameFunc(strings.ToLower), // naming conversion scheme to be used by the clients
  )
```



##### 05 [Session](https://pitaya.readthedocs.io/en/latest/features.html#sessions)

1. 客户端会话，支持异步交互
2. 通常跟uid绑定，个人数据通过session推送到前端



```go
s := pitaya.GetSessionFromCtx(ctx)
// 前端用startx.on("onMemers", onMembers)注册事件处理方法;
s.Push("onMembers", &AllMembers{Members: uids})
```





----

#### 0x09 References

1. https://github.com/topfreegames/pitaya
2. [在线文档](https://pitaya.readthedocs.io/en/latest)