# 网络同步原理和结构

**阅读本文大概需要 15 分钟**

**想要理解网络同步的原理，首先要确定几个问题方向**

1. 网络同步是谁和谁同步？
2. 网络同步都同步什么？
3. 如何实现网络同步？

## 什么是客户端和服务端

### 1.1 客户端（Client）

**客户端**（Client）或称为用户端，是指与服务器相对应，为客户提供本地服务的程序。除了一些只在本地运行的应用程序之外，一般安装在普通的终端上，需要与服务端互相配合运行。

**游戏客户端：**游戏客户端是将游戏中的很多资源储存起来的软件。玩家通过客户端输入指令，客户端翻译成数据发送给服务器，服务器处理完给出结果，然后再由客户端翻译成图形化表现出来告诉玩家。可以说，客户端是一个玩家和服务器之间的中介。

简而言之，对于游戏研发来说，客户端是运行在玩家终端设备中的游戏程序

游戏数据或游戏规则被存储或运行在服务器中的就是**网络游戏**

而不连接服务器，将游戏逻辑、数据等存储在本地的就被称之为**单机游戏**

### 1.2 服务端（Server）

**服务端：**是为客户端服务的，服务的内容诸如向客户端提供资源，保存客户端数据。是实现游戏特色化的重要途径，也是最直接可以通过游戏表现出来的技术，比如你要修改某个 NPC 的参数，重加载后，在游戏内立刻体现出来。

**游戏服务端：**就是为游戏客户端服务的，服务的内容包含为客户端提供登录，保存游戏玩家资料，提供玩家在线游戏，这也是和单机游戏的区别。游戏客户端的登陆，需要服务端的授权，举例：游戏服务端向玩家提供登陆器，而登陆器就是连接服务端的必须工具，登陆器也可以理解为服务端的授权文件。

如上所述，服务端承载着重大的责任，它存储着所有客户端的资源与数据，甚至是游戏逻辑

eg:

1. 《消消乐》这种休闲类游戏的同步机制大部分属于弱联网机制

它可以在没有网络连接的时候进行单机游戏

游戏结束后将游戏结果存储在本地

等待有网络连接后将本地数据上传同步至服务端

1. 《五子棋》这种实时竞技类游戏的同步机制就需要实时同步数据

服务端创建房间，客户端 1 与客户端 2 加入房间

客户端下棋的所有数据都需要经过服务端同步至房间内其他客户端

在客户端下棋的同时服务端要根据研发制定的规则进行胜负的逻辑判断

判断后将结果同步至房间内其他客户端

## 网络同步结构

在编辑器中，理解网络同步之前，首先需要理解一个功能**“静态”**

### 2.1 静态（Static）

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnBGCg5MaBgSbDtePxk2tCRe.png)

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcntPx5dImKvkaJrNjJH8FsDf.png)

如上图所示，勾选“**StaticStatus（静态状态）**”即该对象为静态对象，反之则为非静态对象

静态对象在游戏研发中经常会遇到，字面意思，它代表当前对象为静止的、不动的

也就是说，所有被勾选为静态的对象，都是不可动态更改的

不更改，也就意味着该静态对象只需要在双端进行创建，不需要进行网络同步等操作

eg：

棋类游戏的棋盘不需要进行移动、形变等操作，一般都为静态对象

而棋子则需要进行动态创建、销毁等操作，故棋子一般为非静态对象

### 客户端(C)、服务端(S)、双端(C&S)的区别

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnUuKAKDCutnbUnVukbINa2f.png)

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnSSEiJox833KuozpWvkblae.png)

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcn8w8hBcIjH9Ya8Uahu3MHsg.png)

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnOZisUkv5dKM1L7KcWLZi6d.png)

如上两张图所示，可通过上两种方式将对象的“**NetStatus（网络状态）**”网络状态设置为

客户端（Client）、服务端（Server）、双端（Server and Client）三种模式，

### 2.2 客户端（Client）

网络状态被设置为客户端的对象，只会在客户端创建，服务端不会创建，也不会同步

通常会被设置为客户端的对象有特效、道具模型、UI 资源等等

这些对象一般只需要进行本地渲染、播放、判断等操作

例如：

音效、特效等，只需要在客户端执行创建、播放、停止等操作

### 2.3 服务端（Server）

网络状态被设置为服务端的对象，只会在服务端创建，客户端不会创建，也不会同步

需要服务端主控，防作弊，规则的，不被客户端控制的设置为服务端

例如：

坐标锚点：NPC 寻路功能

客户端不需要存储和判断 NPC 的锚点对象，当寻路的时候，只需要请求服务端发送 NPC 的锚点所在坐标信息、或直接返回其寻路的路径即可

### 2.4 双端（Server and Client）

网络状态被设置为双端的对象，会在服务端先加载并创建

客户端先加载，等待服务端的指令进行创建，然后建立通信

**数据同步以服务端为主**

通常会将需要多端同步（多个客户端同步）的对象设置为双端

例如：

棋子、野怪、载具等等

它们的坐标、颜色、碰撞等交互都需要实时判断且同步

## 如何实现网络同步

### 3.1 客户端 ---> 服务端

#### 3.1.1 客户端发送

/**

* 客户端发送事件给服务器
* @param eventName 事件名
* @param params 事件内容

*/

function dispatchToServer(eventName: string, ...params: unknown[]): DispatchEventResult;

```ts
/** 当脚本被实例后，会在第一帧更新前调用此函数 */
protected async onStart(): Promise`<void>` {
    
    // 客户端执行
    if(SystemUtil.isClient()) {
        //获取当前客户端的玩家对象，方便后续使用
        let player = await Gameplay.asyncGetCurrentPlayer();

        // 声明一个变量准备传递到服务端：这里是一个string类型的字符串
        let message = `你好，我是客户端玩家 ${player.getPlayerID()}`;

        //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
        Events.dispatchToServer("ClientToServer", message);

        //向控制台打印发送行为
        console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: ClientToServer, 包含一条消息 : ${message}`);

    }

    //服务端执行
    if(SystemUtil.isServer()) {
        //服务端监听从客户端派发的事件：事件.添加客户端监听器("事件名"，(发送事件的客户端玩家，客户端发送的内容))；
        Events.addClientListener("ClientToServer", (player: Gameplay.Player, message: string) => {
            
            //向控制台打印监听结果
            console.log(`服务端监听到 玩家: ${player.getPlayerID()} 派发的事件: ClientToServer, 信息内容是：${message}`);
        }); 
    }

}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnX8yBMN8W4pCNsEkJFqzZie.png)

#### 3.1.2 服务端监听

/**

* 服务器监听客户端发来的事件
* @param eventName 事件名
* @param listener 监听回调  Player 发送事件的客户端 target 事件内容
* @return EventListener

*/

function AddClientListener(eventName: string, listener: (player: Gameplay.Player, ...params: unknown[]) => void): EventListener;

```ts
/** 当脚本被实例后，会在第一帧更新前调用此函数 */
protected async onStart(): Promise`<void>` {
    
    // 客户端执行
    if(SystemUtil.isClient()) {
        //获取当前客户端的玩家对象，方便后续使用
        let player = await Gameplay.asyncGetCurrentPlayer();

        // 声明一个变量准备传递到服务端：这里是一个string类型的字符串
        let message = `你好，我是客户端玩家 ${player.getPlayerID()}`;

        //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
        Events.dispatchToServer("ClientToServer", message);

        //向控制台打印发送行为
        console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: ClientToServer, 包含一条消息 : ${message}`);

    }

    //服务端执行
    if(SystemUtil.isServer()) {
        //服务端监听从客户端派发的事件：事件.添加客户端监听器("事件名"，(发送事件的客户端玩家，客户端发送的内容))；
        Events.addClientListener("ClientToServer", (player: Gameplay.Player, message: string) => {
            
            //向控制台打印监听结果
            console.log(`服务端监听到 玩家: ${player.getPlayerID()} 派发的事件: ClientToServer, 信息内容是：${message}`);
        }); 
    }

}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnBXFespqzAAn2WuwOMtCvVg.png)

### 3.2 服务端 ---> 客户端

#### 3.2.1 服务端发送 - 单一客户端

/** 服务器发送事件给指定客户端

* @param player 客户端
* @param eventName 事件名
* @param params 事件内容

*/

function dispatchToClient(player: Gameplay.Player, eventName: string, ...params: unknown[]): DispatchEventResult;

```ts
/** 当脚本被实例后，会在第一帧更新前调用此函数 */
protected async onStart(): Promise`<void>` {

    // 客户端执行
    if(SystemUtil.isClient()) {
        //获取当前客户端的玩家对象，方便后续使用
        let player = await Gameplay.asyncGetCurrentPlayer();

        // 声明一个变量准备传递到服务端：这里是一个string类型的字符串
        let message = `你好，我是客户端玩家 ${player.getPlayerID()}`;

        //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
        Events.dispatchToServer("ClientToServer", message);

        //向控制台打印发送行为
        console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: ClientToServer, 内容为 : ${message}`);

         //客户端监听从服务端派发的事件：事件.添加服务端事件监听器("事件名"，(服务端发送的内容))；
         Events.addServerListener("ServerToClient", (reply: string) => {
            
            //向控制台打印监听结果
            console.log(`玩家: ${player.getPlayerID()} 客户端监听到服务端派发的事件: ServerToClient, 信息内容是：${reply}`);

        }); 
    }

    //服务端执行
    if(SystemUtil.isServer()) {
        //服务端监听从客户端派发的事件：事件.添加客户端事件监听器("事件名"，(发送事件的客户端玩家，客户端发送的内容))；
        Events.addClientListener("ClientToServer", (player: Gameplay.Player, message: string) => {
            
            //向控制台打印监听结果
            console.log(`服务端监听到 玩家 ${player.getPlayerID()} 派发的事件: ClientToServer, 内容是：${message}`);

             // 声明一个变量准备传递到客户端：这里是一个string类型的字符串
            let reply = `你好，我是服务端，已收到客户端玩家 ${player.getPlayerID()} 发来的消息`;
            
            //从服务端派发消息到指定的客户端：事件.派发到服务端(指定的客户端, "事件名"，事件内容)；
            Events.dispatchToClient(player, "ServerToClient", reply);

             //向控制台打印发送行为
            console.log(`服务端向玩家 ${player.getPlayerID()} 派发了一个事件: ServerToClient, 内容为 : ${reply}`);

        }); 
    }

}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnJkMRzw7x5NwNbTYZja3Clm.png)

#### 3.2.2 服务端发送 - 所有客户端

/** 服务器发送事件给所有客户端（以 Player 为中心的同屏范围）

* @param player 客户端
* @param eventName 事件名
* @param params 事件内容

*/

function dispatchToAllClient(player: Gameplay.Player, eventName: string, ...params: unknown[]): DispatchEventResult;

```ts
/** 当脚本被实例后，会在第一帧更新前调用此函数 */
protected async onStart(): Promise`<void>` {

    // 客户端执行
    if(SystemUtil.isClient()) {
        //获取当前客户端的玩家对象，方便后续使用
        let player = await Gameplay.asyncGetCurrentPlayer();

        // 声明一个变量准备传递到服务端：这里是一个string类型的字符串
        let message = `你好，我是客户端玩家 ${player.getPlayerID()}`;

        // 按下键盘G键
        InputUtil.onKeyDown(Type.Keys.G, () => {
            //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
            Events.dispatchToServer("ClientToServer", message);

            //向控制台打印发送行为
            console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: ClientToServer, 内容为 : ${message}`);
        });
       

         //客户端监听从服务端派发的事件：事件.添加服务端事件监听器("事件名"，(服务端发送的内容))；
         Events.addServerListener("ServerToAllClient", (reply: string) => {
            
            //向控制台打印监听结果
            console.log(`玩家: ${player.getPlayerID()} 客户端监听到服务端派发的事件: ServerToAllClient, 信息内容是：${reply}`);

        }); 
    }

    //服务端执行
    if(SystemUtil.isServer()) {
        //服务端监听从客户端派发的事件：事件.添加客户端事件监听器("事件名"，(发送事件的客户端玩家，客户端发送的内容))；
        Events.addClientListener("ClientToServer", (player: Gameplay.Player, message: string) => {
            
            //向控制台打印监听结果
            console.log(`服务端监听到 玩家 ${player.getPlayerID()} 派发的事件: ClientToServer, 内容是：${message}`);

             // 声明一个变量准备传递到客户端：这里是一个string类型的字符串
            let reply = `你好，我是服务端，已收到客户端玩家 ${player.getPlayerID()} 发来的消息`;
            
            //从服务端派发消息到所有客户端：事件.派发到服务端("事件名"，事件内容)；
            Events.dispatchToAllClient("ServerToAllClient", reply);

             //向控制台打印发送行为
            console.log(`服务端向所有客户端派发了一个事件: ServerToAllClient, 内容为 : ${reply}`);

        }); 
    }

}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnTlGly7IJadX7kFfWrIhmkb.png)

#### 3.2.4 客户端监听

/**

* 客户端监听服务器事件
* @param eventName 事件名
* @param listener 监听回调 params 事件内容
* @return EventListener

*/

function AddServerListener(eventName: string, listener: ((...params: unknown[]) => void)): EventListener;

```ts
/** 当脚本被实例后，会在第一帧更新前调用此函数 */
protected async onStart(): Promise`<void>` {

    // 客户端执行
    if(SystemUtil.isClient()) {
        //获取当前客户端的玩家对象，方便后续使用
        let player = await Gameplay.asyncGetCurrentPlayer();

        // 声明一个变量准备传递到服务端：这里是一个string类型的字符串
        let message = `你好，我是客户端玩家 ${player.getPlayerID()}`;

        // 按下键盘G键
        InputUtil.onKeyDown(Type.Keys.G, () => {
            //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
            Events.dispatchToServer("ClientToServer", message);

            //向控制台打印发送行为
            console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: ClientToServer, 内容为 : ${message}`);
        });
       

         //客户端监听从服务端派发的事件：事件.添加服务端事件监听器("事件名"，(服务端发送的内容))；
         Events.addServerListener("ServerToAllClient", (reply: string) => {
            
            //向控制台打印监听结果
            console.log(`玩家: ${player.getPlayerID()} 客户端监听到服务端派发的事件: ServerToAllClient, 信息内容是：${reply}`);

        }); 
    }

    //服务端执行
    if(SystemUtil.isServer()) {
        //服务端监听从客户端派发的事件：事件.添加客户端事件监听器("事件名"，(发送事件的客户端玩家，客户端发送的内容))；
        Events.addClientListener("ClientToServer", (player: Gameplay.Player, message: string) => {
            
            //向控制台打印监听结果
            console.log(`服务端监听到 玩家 ${player.getPlayerID()} 派发的事件: ClientToServer, 内容是：${message}`);

             // 声明一个变量准备传递到客户端：这里是一个string类型的字符串
            let reply = `你好，我是服务端，已收到客户端玩家 ${player.getPlayerID()} 发来的消息`;
            
            //从服务端派发消息到所有客户端：事件.派发到服务端("事件名"，事件内容)；
            Events.dispatchToAllClient("ServerToAllClient", reply);

             //向控制台打印发送行为
            console.log(`服务端向所有客户端派发了一个事件: ServerToAllClient, 内容为 : ${reply}`);

        }); 
    }

}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnKoHcvY6ingMsk9ZHLPrW2g.png)

## 使用 replicated 实现属性同步

eg：

```ts
@Core.Class
export default class NewScript0 extends Core.Script {

    @Core.Property({replicated: true, onChanged: "onChanged"})
    testNumber: number = 10; 

    onChanged(): void{
        console.log(`testNumber的值发生了改变，当前值为 ${this.testNumber}`);
    }

    /** 当脚本被实例后，会在第一帧更新前调用此函数 */
    protected async onStart(): Promise`<void>` {

        // 客户端执行
        if(SystemUtil.isClient()) {
            //获取当前客户端的玩家对象，方便后续使用
            let player = await Gameplay.asyncGetCurrentPlayer();

            // 按下键盘G键
            InputUtil.onKeyDown(Type.Keys.G, () => {
                //从客户端派发消息到服务端：事件.派发到服务端("事件名"，事件内容)；
                Events.dispatchToServer("addTestNumber");

                //向控制台打印发送行为
                console.log(`玩家: ${player.getPlayerID()} 向服务端派发了一个事件: addTestNumber`);
            });
        
        }

        //服务端执行
        if(SystemUtil.isServer()) {

            console.log(`testNumber的初始值为 ${this.testNumber}`);

            //服务端监听从客户端派发的事件：事件.添加客户端事件监听器("事件名"，(发送事件的客户端玩家))；
            Events.addClientListener("addTestNumber", (player: Gameplay.Player) => {
                
                //向控制台打印监听结果
                console.log(`服务端监听到 玩家 ${player.getPlayerID()} 派发的事件: addTestNumber, 服务端将testNumber加1`);

                this.testNumber += 1;

            }); 
        }

    }

    /**
     * 周期函数 每帧执行
     * 此函数执行需要将this.bUseUpdate赋值为true
     * @param dt 当前帧与上一帧的延迟 / 秒
     */
    protected onUpdate(dt: number): void {

    }

    /** 脚本被销毁时最后一帧执行完调用此函数 */
    protected onDestroy(): void {

    }
}
```

![](https://wstatic-a1.233leyuan.com/productdocs/static/boxcnwQuYPcPp7JCfOHVLYPK5Ke.png)

#### 注意事项：

- 使用类必须使用 Class 或 GameObject 注解标注
- 必须派生自 Script 或 GameObject 类型
- 使用 Property 注解对需要同步属性标注
- 注册属性同步回调函数
- 同步属性只在 Server 端修改才会触发
