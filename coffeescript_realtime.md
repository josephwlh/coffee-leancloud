# JavaScript 实时通信 SDK

## 简介

感谢你使用 JavaScript 的实时通信 SDK，LeanCloud 的实时通信服务每天处理请求数超过百万级，安全可靠，你的明智之选。

你可以通过使用我们提供的 SDK，一行后端代码都不用写，就可以做一个功能完备的实时聊天应用、也可以做一个实时对战类的游戏，总之一切与实时通信相关的业务都可以使用 LeanCloud 提供的实时通信服务。

你还可以通过实时通信 SDK 配合「[云代码](https://leancloud.cn/docs/cloud_code_guide.html)」简单的实现之前可能需要很多人才能完成的实时通信相关需求的开发，并且如果你达到我们的收费额度，也会以极低的成本支付你的使用费用，成本远远小于同等规模自建实时通信服务。

本 SDK 实现轻量、高效、无依赖，支持移动终端的浏览器及各种 WebView，包括可以使用在微信的 WebView 中。 

## 概念

在你开始使用之前，首先来了解一下实时通信 SDK 的基本结构。实时通信在 SDK 里面分为两个层次：

* 一层是底层实时通信基础模块，负责与服务器匹配和授权，建立基础的连接和底层控制。这个层次只会派发一些基础的事件出来，你可以通过 SDK 监听这些事件。这个层面 SDK 会保证底层连接的稳定，包括断开重试，心跳连接等策略；

* 另一层是业务逻辑层，用户可以使用 SDK 建立不同的 Conversation（对话）。一个 Conversation 就是一个独立的通信单元，但 Conversation 间一般是无法通信的。当然你可以自己在业务逻辑层，通过派发自定义事件的方式来封装其他自定义的逻辑。当创建一个新 Conversation 之后，对应的服务器端就会自动生成这个 Conversation，除非你自行删除，否则该 Conversation 一直存在。但是用户如果没有连接，该房间不会占用服务器资源，只是存储的一个数据条目；

## 特别说明

Conversation（对话）这个概念有些人更喜欢叫做 Room（房间），就是几个客户端节点在通信之前要放到同一个房间中，其实这两个是一个道理，只是名字不同，SDK 中为了让大家好理解，两个名字都可以使用。如果你觉得更喜欢 Room 这个概念，那就可以使用 room 方法创建 Room，如果喜欢 Conversation，那就使用 conv 方法创建 Conversation。

也许说到这里你已经跃跃欲试了，好的，那你可以试用一下看看了。

注：如果还不是很了解 LeanCloud 的使用方式，建议先从「[快速入门](https://leancloud.cn/start.html)」开始尝试。

## 通过 bower 安装

运行命令：
```
bower install leancloud-realtime -- save
```
安装之后，页面直接加载 bower_components/leancloud-realtime/src/AV.realtime.js 即可。

[什么是 bower ?](http://bower.io/)

## Github 仓库地址

可以直接通过 Github 仓库使用 SDK，也可以通过 Github 给我们提出您的建议

Github 仓库地址：[https://github.com/leancloud/js-realtime-sdk](https://github.com/leancloud/js-realtime-sdk)

所有的 Release 地址: [https://github.com/leancloud/js-realtime-sdk/releases](https://github.com/leancloud/js-realtime-sdk/releases)

## 文档贡献

如果觉得这个文档写的不够好，也可以帮助我们来不断完善。

Github 仓库地址：[https://github.com/leancloud/docs](https://github.com/leancloud/docs)

## Demo 及示例代码

如果您觉得一点点阅读文档较慢，可以直接看我们的「[Demo 代码](https://github.com/leancloud/js-realtime-sdk/tree/master/demo)」，并且下载自己运行一下试试看，Demo 代码可以通过开两个浏览器标签的方式来模拟两个用户的互相通信，代码中也有详细的注释方便你来了解使用方法。

```coffeescript
# 最简的示例代码，请换成自己的 appId，可以通过浏览器多个标签模拟多用户通信
appId = '9p6hyhh60av3ukkni3i9z53q1l8y'
# clientId 就是实时通信中的唯一用户 id
clientId = 'LeanCloud01'
realtimeObj = undefined
conversationObj = undefined
# 创建实时通信实例（支持单页多实例）
realtimeObj = AV.realtime(
  appId: appId
  clientId: clientId)
# 当前 SDK 版本
console.log '当前 SDK 版本是 ' + AV.realtime.version
# 实时通信服务连接成功
realtimeObj.on 'open', ->
  console.log '实时通信服务建立成功！'
  # 创建一个聊天室，conv 是 conversation 的缩写，也可以用 room 方法替换
  conversationObj = realtimeObj.conv({
    members: [ 'LeanCloud02' ]
    data:
      name: 'LeanCloud'
      m: 123
  }, (data) ->
    if data
      console.log 'Conversation 创建成功!', data
  
  )

# 当聊天断开时触发
realtimeObj.on 'close', ->
  console.log '实时通信服务被断开！'

# 接收断线或者网络状况不佳的事件（断网可测试）
realtimeObj.on 'reuse', ->
  console.log '正在重新连接。。。'

# 当 Conversation 被创建时触发，当然您可以使用回调函数来处理，不一定要监听这个事件
realtimeObj.on 'create', (data) ->
  # 向这个 Conversation 添加新的用户
  conversationObj.add [
    'LeanCloud03'
    'LeanCloud04'
  ], (data) ->
    console.log '成功添加用户：', data
  
  # 从这个 Conversation 中删除用户
  conversationObj.remove 'LeanCloud03', (data) ->
    console.log '成功删除用户：', data
  
  # 向这个 Conversation 中发送消息
  conversationObj.send { abc: 123 }, (data) ->
    console.log '发送的消息服务端已收收到：', data
  
  setTimeout (->
    # 查看历史消息
    conversationObj.log (data) ->
      console.log '查看当前 Conversation 最近的聊天记录：', data
    
  
  ), 2000
  # 当前 Conversation 接收到消息
  conversationObj.receive (data) ->
    console.log '当前 Conversation 收到消息：', data
  
  # 获取当前 Conversation 中的成员信息
  conversationObj.list (data) ->
    console.log '列出当前 Conversation 的成员列表：', data
  
  # 取得当前 Conversation 中的人数
  conversationObj.count (num) ->
    console.log '取得当前的用户数量：' + num
  

# 监听所有用户加入的情况
realtimeObj.on 'join', (data) ->
  console.log '有用户加入某个当前用户在的 Conversation：', data

# 监听所有用户离开的情况
realtimeObj.on 'left', (data) ->
  console.log '有用户离开某个当前用户在的 Conversation：', data

# 监听所有 Conversation 中发送的消息
realtimeObj.on 'message', (data) ->
  console.log '某个当前用户在的 Conversation 接收到消息：', data

```

## 安全

### 安全域名

如果是纯前端使用 JavaScript SDK，请务必配置「控制台」-「设置」-「基本信息」-「JavaScript 安全域名」，防止其他人盗用你的服务器资源。实时通信的安全域名设置会有三分钟的延迟，所以设置完毕后，请耐心等待下。

详细请看 JavaScript 指南中的「[安全域名](https://leancloud.cn/docs/js_guide.html#安全域名)」部分。

### 权限和认证

为了满足开发者对权限和认证的需求，我们设计了签名的概念。

详细请看实时通信开发指南中的 「[权限和认证](https://leancloud.cn/docs/realtime.html#权限和认证)」部分。

## 方法列表

### 全局命名空间

LeanCloud JavaScript 相关 SDK 都会使用「AV」作为命名空间。

### AV.realtime

使用:
```coffeescript
AV.realtime(options, callback)
```

描述：

* 这是创建实时通信对象的方法，会启动实时通信的连接。自动调用 open 方法，内部与服务器匹配，并建立 WebSocket 连接。内部会自动维持与服务器的链接稳定，控制心跳数据包的频率，超时检测等，如果发生中断可以通过监听对应的事件来给用户界面上的变化提示。

* 另外，此方法支持多实例，也就是说，你可以在一个页面中，创建多个 RealtimeObject 来实现聊天。

参数：

* options {Object} （必须） 配置实时通信服务所需的必要参数。其中包括：

    * appId {String} （必须）应用的 AppId，在「控制台」-「设置」-「基本信息」中可以查看；

    * clientId {String} （必须）当前客户端的唯一 id，用来标示当前客户端；

    * authFun {Function}（可选）可以传入权限认证的方法，每次当建立连接的时候就会去服务器请求认证，或者许可之后才能建立连接，详细阅读「[权限和认证](https://leancloud.cn/docs/realtime.html#权限和认证)」相关文档，也可以参考 [demo](https://github.com/leancloud/js-realtime-sdk/tree/master/demo) 中的示例；

返回：

* {Object} 返回 RealtimeObject（实时通信对象），其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime({
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123'
}, ->
  console.log '与服务器连接成功！'

)
# 监听 open 事件会得到同样的效果
realtimeObject.on 'open', ->
  console.log '与服务器连接成功！'

```

### AV.realtime.version

用法：
```coffeescript
AV.realtime.version
```

描述：

* 获取当前 SDK 的版本信息

返回：

* {String} 返回当前版本

例子：

```coffeescript
// 返回版本号
console.log('当前版本是：' + AV.realtime.version);   
```

### RealtimeObject.open

用法：
```coffeescript
RealtimeObject.open(callback)
```

描述：

* 该方法一般情况下，你不需要调用，SDK 会自动启动与服务的连接。该方法可以启动实时通信的连接，与服务器匹配建立 websocket 连接；

参数：

* callback {Function}（可选）创建成功并且与服务器建立连接后触发的回调，此时也会派发一个私有的事件「open」到 RealtimeObject 内部，也可以通过监听当前的 RealtimeObject 实例的 open 事件来处理连接成功的业务逻辑；

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 真正使用时这里也无需调用，实例化 RealtimeObject 的时候 SDK 会自动调用 open 方法
realtimeObject.open ->
  # 与服务器连接成功
  console.log 'open'

realtimeObject.on 'open', ->
  console.log 'open,too.'

```

### RealtimeObject.close

用法：
```coffeescript
RealtimeObject.close()
```

描述：

* 关闭实时通信的连接，并且内部会关闭 websocket 连接。该方法没有回调，因为调用会立刻关闭 WebSocket。

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
realtimeObject.close()
realtimeObject.on 'close', ->
  console.log '与服务器已经断开！'

```

### RealtimeObject.on

用法：
```coffeescript
RealtimeObject.on(eventName, callback)
```

描述：

* 监听 RealtimeObject 内部的事件，基于一个局部的事件中心，事件每次派发就会被触发一次；

参数：

* eventName {String} （必须）监听的事件名称

* callback {Function} （必须）当事件被派发时会调用的回调

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 当新建一个 Room 的时候就会触发
realtimeObject.on 'create', (data) ->
  console.log data

# 有人加入 Room 的时候会被触发
realtimeObject.on 'join', (data) ->
  console.log data

```

### RealtimeObject.once

用法：
```coffeescript
RealtimeObject.once(eventName, callback)
```

描述：

* 监听 RealtimeObject 内部的事件，基于一个局部的事件中心，事件只会被触发一次；

参数：

* eventName {String} （必须）监听的事件名称

* callback {Function} （必须）当事件被派发时会调用的回调

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 当服务建立之后会被触发
realtimeObject.once 'open', ->
  console.log 'opened'

# 当服务关闭的时候会被触发
realtimeObject.once 'close', ->
  console.log 'closed'


```

### RealtimeObject.emit

用法：
```coffeescript
RealtimeObject.emit eventName, dataObject
```

描述：

* 派发一个事件到 RealtimeObject 中，局部的事件中心

参数：

* eventName {String} （必须）派发的事件名称

* dataObject {Obejct}（可选）传递的参数，可以在监听的回调中通过第一个参数获取

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 当事件被派发的时候会触发
realtimeObject.on 'LeanCloud123', (data) ->
  # 会输出 test
  console.log data.aaa

# 派发了一个自定义的事件，名字叫「LeanCloud123」。
realtimeObject.emit 'LeanCloud123', aaa: 'test'
```

### RealtimeObject.off

用法：
```coffeescript
RealtimeObject.off eventName, callback
```

描述：

* 从 RealtimeObject 中的私有的事件中心，删除一个事件对应的回调函数绑定

参数：

* eventName {String} （必须）一个绑定过的事件名称

* callback {Function}（必须）要在这个事件中移除的函数

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')

eventFun = (data) ->
  # 会输出 test
  console.log data.aaa


# 当事件被派发的时候会触发
realtimeObject.on 'LeanCloud123', eventFun
# 事件方法从事件监听中删除
realtimeObject.off 'LeanCloud123', eventFun
# 派发了一个自定义的事件，名字叫「LeanCloud123」。
realtimeObject.emit 'LeanCloud123', aaa: 'test'
```

### RealtimeObject.conv

用法：
```coffeescript
RealtimeObject.conv options, callback
```

描述：

* 创建一个 Conversation（对话），实时通信的最小单元。conv 和 room 方法实现的是同样的方法，为了保持概念上的统一，详见「[特别说明](#特别说明)」；

参数：

* options {Object} （必须）传入配置信息

    * data {Object} （可选）自定义的数据信息，如 title、name 等
    
    * callback {Function} （可选）创建成功后的回调函数，此时也会在 RealtimeObject 内部派发一个 create 事件，可以通过 RealtimeObject.on() 方法来监听；

返回：

* {Object} 返回 convObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.conv({
  members: [ 'LeanCloud02' ]
  data: title: 'testTitle'
}, (result) ->
  console.log 'Conversation created callback'

)
# 当新 Room 被创建时触发
realtimeObject.on 'create', (data) ->
  console.log data

```

### RealtimeObject.conv

用法：
```coffeescript
RealtimeObject.conv(convId, callback)
```

描述：

* 匹配一个在服务器端已有的 Conversation（对话），并生成对应的 convObject，此时不派发任何事件；

参数：

* convId {String} （必须）传入已有 Conversation（对话） 的 id

* callback {Function} （可选）创建成功后的回调函数，此时不会派发任何事件；

返回：

* {Object} 返回 convObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
convId = 'sasfalklkjdlfs123'
conv = realtimeObject.conv(convId)
```

### RealtimeObject.room

用法：
```coffeescript
RealtimeObject.room(options, callback)
```

描述：

* 创建一个 Room（房间），实时通信的最小单元。room 方法就是 conv 方法的一个别名，为了保持概念的统一，详见「[特别说明](#特别说明)」，使用方式和 conv 完全相同。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room({
  members: [ 'LeanCloud02' ]
  data: title: 'testTitle'
}, (result) ->
  console.log 'Room created callback'

)

// 当新 Room 被创建时触发
realtimeObject.on 'create', (data) ->
  console.log data

```

### RealtimeObject.room

用法：
```coffeescript
RealtimeObject.room(roomId, callback)
```

描述：

* 匹配一个在服务器端已有的 room，并生成对应的 RoomObject，此时不派发任何事件；room 方法就是 conv 方法的一个别名，为了保持概念的统一，详见「[特别说明](#特别说明)」，使用方式和 conv 完全相同。

参数：

* roomId {String} （必须）传入已有 Room 的 id

* callback {Function} （可选）创建成功后的回调函数，此时不会派发任何事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
roomId = 'sasfalklkjdlfs123'
room = realtimeObject.room(roomId)
```

### RealtimeObject.query

用法：
```coffeescript
RealtimeObject.query(callback)
```

描述：

* 获取当前用户所在的 Room 信息

参数：

* callback {Function} （必须）创建成功后的回调函数，参数中可以获取到 Room 的列表；

返回：

* {Object} 返回 realtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 当实时通信建立成功之后
realtimeObject.on 'open', ->
  # 查询当前用户所在的组
  realtimeObject.query (data) ->
    console.log data
```

### RealtimeObject.query

用法：
```coffeescript
RealtimeObject.query(options, callback)
```

描述：

* 查询实时通信表中的数据

参数：

* options {Object} （可选）一些配置参数

  * where {Object} （可选）默认为包含自己的查询 {m: clientId}

  * sort {String} （可选）默认为 -lm，最近对话反序

  * limit {Number} （可选）一次获取的条目数量，默认为 10

  * skip {Number} （可选）跳过多少个索引，比如 skip: 1000，就是从 1001 开始查询，默认为 0

  * compact {Boolean} （可选）是否要去掉内置大字段（成员列表，静音列表和当前用户静音的状态），默认 false

* callback {Function} （必须）创建成功后的回调函数，参数中可以获取到 Room 的列表

返回：

* {Object} 返回 RealtimeObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
# 当实时通信建立成功之后
realtimeObject.on 'open', ->
  # 各种条件查询
  realtimeObject.query {
    where: m: 'abc123'
    sort: '-lm'
    limit: 100
    skip: 200
    compact: false
  }, (data) ->
    console.log data
```

### RoomObject.ping

用法：
```coffeescript
RoomObject.ping(clientIdList, callback)
```

描述：

* 查询对应的 clientId 是否处于服务在线状态

参数：

* clientIdList {Array} （必须）传入已有用户的 clientId 的数组，如 ['LeanCloud1', 'LeanCloud2']

* callback {Function} （必须）回调函数，可以在参数中获得在线的 clientIdList，比如返回 ['LeanCloud2']，则说明 LeanCloud2 在线

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud01'
    'LeanCloud02'
  ]
  data: title: 'testTitle')
rt.ping [
  'LeanCloud01'
  'LeanCloud02'
], (data) ->
  # 返回传入的 id 中，在线的用户 id
  console.log data
```

### RoomObject.ping

用法：
```coffeescript
RoomObject.ping(clientId, callback)
```

描述：

* 查询对应的 clientId 是否处于服务在线状态

参数：

* clientId {String} （必须）传入已有用户的 clientId，如 'LeanCloud1'

* callback {Function} （必须）回调函数，可以在参数中获得在线的 clientIdList，比如返回 ['LeanCloud1']，则说明 LeanCloud1 在线

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud01'
    'LeanCloud02'
  ]
  data: title: 'testTitle')
rt.ping 'LeanCloud01', (data) ->
  if data.length
    console.log '用户在线'
  else
    console.log '用户不在线'
```

### RoomObject.add

用法：
```coffeescript
RoomObject.add(clientId, callback)
```

描述：

* 向当前 RoomObject 中添加一个用户

参数：

* clientId {String} （必须）传入已有用户的 clientId

* callback {Function} （可选）创建成功后的回调函数，此时会在 RealtimeObject 内部派发一个 join 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [ 'LeanCloud02' ]
  data: title: 'testTitle')
room.add 'LeanCloud03', ->
  console.log 'Add success.'

# 当前 Room 有新的 client 加入时触发
realtimeObject.on 'join', (data) ->
  console.log data
```

### RoomObject.add

用法：
```coffeescript
RoomObject.add(clientIdList, callback)
```

描述：

* 向当前 RoomObject 中添加多个用户

参数：

* clientIdList {Array} （必须）传入已有用户的 clientId 的 list，每个元素是 client

* callback {Function} （可选）创建成功后的回调函数，此时会在 RealtimeObject 内部派发一个 join 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [ 'LeanCloud02' ]
  data: title: 'testTitle')
room.add [
  'LeanCloud03'
  'LeanCloud04'
], ->
  console.log 'Add success.'

# 当前 Room 有新的 client 加入时触发
realtimeObject.on 'join', (data) ->
  console.log data
```

### RoomObject.remove

用法：
```coffeescript
RoomObject.remove(clientId, callback)
```

* 描述：从当前 RoomObject 中删除一个用户

参数：

* clientId {String} （必须）传入已有用户的 clientId

* callback {Function} （可选）删除成功后的回调函数，此时会在 RealtimeObject 内部派发一个 left 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [ 'LeanCloud02' ]
  data: title: 'testTitle')
room.remove 'LeanCloud02', ->
  console.log 'Remove success.'

# 当前 Room 有 client 立刻时触发
realtimeObject.on 'left', (data) ->
  console.log data
```

### RoomObject.remove

用法：
```coffeescript
RoomObject.remove(clientIdList, callback)
```

描述：

* 从当前 RoomObject 中删除多个用户

参数：

* clientIdList {Array} （必须）传入已有用户的 clientId 的 list，每个元素是 client

* callback {Function} （可选）创建成功后的回调函数，此时会在 RealtimeObject 内部派发一个 left 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
room.remove [
  'LeanCloud02'
  'LeanCloud03'
], ->
  console.log 'Remove success.'

# 当前 Room 有 client 立刻时触发
realtimeObject.on 'left', (data) ->
  console.log data
```

### RoomObject.join

用法：
```coffeescript
RoomObject.join(callback)
```

描述：

* 加入当前这个 Room

参数：

* callback {Function} （可选）加入成功后的回调函数，此时会在 RealtimeObject 内部派发一个 join 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room('safjslakjlfkjla123')
room.join ->
  console.log 'join'

# 当前 Room 有新的 client 加入时触发
realtimeObject.on 'join', (data) ->
  console.log data

```

### RoomObject.leave

用法：
```coffeescript
RoomObject.leave(callback)
```

描述：

* 从当前 RoomObject 中离开

参数：

* clientIdList {Array} （必须）传入已有用户的 clientId 的 list，每个元素是 client

* callback {Function} （可选）创建成功后的回调函数，此时会在 RealtimeObject 内部派发一个 left 事件；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
room.leave()
# 当前 Room 有 client 立刻时触发
realtimeObject.on 'left', (data) ->
  console.log data
```

### RoomObject.list

用法：
```coffeescript
RoomObject.list(callback)
```

描述：

* 获取当前 RoomObject 中的成员列表

参数：

* callback {Function} （必须）获取成员列表的回调函数；

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
room.list (data) ->
  console.log data
  # room 中成员 list
``` 

### RoomObject.send

用法：
```coffeescript
RoomObject.send(dataObject, callback)
```

描述：

* 向当前这个 RoomObject 中发送消息

参数：

* dataObject {Object} （必须）发送的数据内容

* callback {Function} （可选）发送到服务器成功后的回调函数，不一定对方已经接收了，但是服务器已经收到。

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
room.send { testMsg: 'abcde' }, ->
  console.log 'server ack.'

# 当前用户所在的组，有消息时触发
realtimeObject.on 'message', (data) ->
  console.log data
```

### RoomObject.receive

用法：
```coffeescript
RoomObject.receive(callback)
```

描述：

* 接收到当前这个 RoomObject 中的消息

参数：

* callback {Function} （必须）收到当前 Room 中信息的处理函数

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
# 当前用户所在的组，有消息时触发
room.receive (data) ->
  console.log data
```

### RoomObject.receipt

用法：
```coffeescript
RoomObject.receipt(callback)
```

描述：

* 如果你通过 RoomObject.send 方法发送了需要有回执功能的信息，那么通过 RoomObject.receipt 可以接收当前这个房间中的所有这类回执信息；回执表示从实时通信服务本身，对方的客户端已经收到该信息

参数：

* callback {Function} （必须）收到的回执信息

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'abc123')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
room.send { abc: 123 }, { receipt: true }, (data) ->
  console.log '信息发送成功，该信息会获取阅读回执'

# 当前用户所在的组，有消息时触发
room.receipt (data) ->
  console.log data
  # 已经收到的 clientId
```
### RoomObject.count

用法：
```coffeescript
RoomObject.count(callback)
```

描述：

* 获取当前这个 Room（或者 Conversation）中的用户数量

参数：

* callback {Function} （必须）返回的数据中可以获取到用户数量

返回：

* {Object} 返回 RoomObject，其中有后续调用的方法，支持链式调用。

例子：

```coffeescript
realtimeObject = AV.realtime(
  appId: '9p6hyhh60av3ukkni3i9z53q1l8y'
  clientId: 'LeanCloud01')
room = realtimeObject.room(
  members: [
    'LeanCloud02'
    'LeanCloud03'
  ]
  data: title: 'testTitle')
# 当前用户所在的组，有消息时触发
room.count (data) ->
  # 当前用户数量
  console.log data
```

## 全局事件

SDK 会默认派发一些事件，这些事件仅会在 RealtimeObject 内部被派发（注意：RoomObject 内部默认不会派发任何事件），你可以通过监听这些事件来完成你的操作。这些事件往往都是脱离 Room（或者 Conversation）的，你可以监听到其他 Room 中的相关信息。

以下是默认事件的说明：

### open

描述：

* 与服务器建立好连接之后就会被派发，包括当服务断开重新被连接上时也会被触发

### close

描述：

* 与服务器连接断开就会被派发，包括网络中断

### create

描述：

* 新建一个 Room 成功之后会被触发

### join

描述：

* 当一个 Room 新增了一个成员之后会被触发

### left

描述：

* 当一个 Room 中有成员离开之后会被触发

### message

描述：

* 当收到消息时会被触发，收到的消息是当前客户端（clientId）存在的 Room 中的信息，所有这些数据都可以在服务器端看到。

### reuse

* 发生连接错误，可能是网络原因，SDK 在自动尝试重连。可以监听这个状态，给用户「服务器已断开，正在重新连接。。。」之类的提示。

### receipt

* 收到消息回执的时候会被触发
