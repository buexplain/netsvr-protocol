# netsvr-protocol

这套`proto`是[https://github.com/buexplain/netsvr](https://github.com/buexplain/netsvr)的网关层与业务层的交互协议。

指令有如下三种类别：

1. 网关单向转发给业务进程的指令
2. 业务进程单向请求网关的指令
3. 业务进程请求网关，网关处理完毕再响应给业务进程的指令

### 网关单向转发给业务进程的指令

| 名称     | 编号                                                                                   | proto                                                                                     | 说明                 |
|--------|--------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|--------------------|
| 客户连接打开 | [ConnOpen](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L30)  | [connOpen.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connOpen.proto)   | 网关转发客户连接打开的信息到业务进程 |
| 客户连接关闭 | [ConnClose](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L31) | [connClose.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connClose.proto) | 网关转发客户连接关闭的信息到业务进程 |
| 透传客户数据 | [Transfer](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L32)  | [transfer.proto](https://github.com/buexplain/netsvr-protocol/blob/main/transfer.proto)   | 网关转发客户连接发来的数据到业务进程 |

### 业务进程单向请求网关的指令

| 名称                  | 编号                                                                                           | proto                                                                                                     | 说明                                                                                |
|---------------------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| 更新连接的信息             | [ConnInfoUpdate](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L36)    | [connInfoUpdate.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connInfoUpdate.proto)       | 每个客户连接都可以在网关中存储：唯一id、主题标签、session信息，该指令用于设置这些信息                                   |
| 删除连接的信息             | [ConnInfoDelete](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L37)    | [connInfoDelete.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connInfoDelete.proto)       | 删除客户连接的：唯一id、主题标签、session信息                                                       |
| 广播                  | [Broadcast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L38)         | [broadcast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/broadcast.proto)                 | 给网关中所有客户连接都发送一条信息                                                                 |
| 组播                  | [Multicast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L39)         | [multicast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/multicast.proto)                 | 给指定的某几个客户连接发送一条信息                                                                 |
| 单播                  | [SingleCast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L40)        | [singleCast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/singleCast.proto)               | 给指定的某个客户连接发送一条信息                                                                  |
| 批量单播                | [SingleCastBulk](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L41)    | [singleCastBulk.proto](https://github.com/buexplain/netsvr-protocol/blob/main/singleCastBulk.proto)       | 将多对单播数据打包成一个块，一次性发送给网关                                                            |
| 订阅主题                | [TopicSubscribe](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L42)    | [topicSubscribe.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicSubscribe.proto)       | 给客户连接打上一些标签，比如客户加入的群的id                                                           |
| 取消订阅的主题             | [TopicUnsubscribe](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L43)  | [topicUnsubscribe.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUnsubscribe.proto)   | 删除客户连接的标签，比如客户退群了，可以将之前标记的群id删除                                                   |
| 删除主题                | [TopicDelete](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L44)       | [topicDelete.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicDelete.proto)             | 从有此标签的客户连接身上删除该标签                                                                 |
| 根据主题发布消息            | [TopicPublish](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L45)      | [topicPublish.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicPublish.proto)           | 根据标签找到所有含此标签的客户连接，并将信息发送给它们                                                       |
| 强制关闭某几个连接           | [ForceOffline](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L46)      | [forceOffline.proto](https://github.com/buexplain/netsvr-protocol/blob/main/forceOffline.proto)           | 强制关闭某个客户连接，比如客户在多个设备连接到网关，则可用此指令去强制关闭客户其它设备的连接                                    |
| 强制关闭某几个空session值的连接 | [ForceOfflineGuest](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L47) | [forceOfflineGuest.proto](https://github.com/buexplain/netsvr-protocol/blob/main/forceOfflineGuest.proto) | 强制关闭某个空session值的连接，比如客户连接到网关，但是并没有发起账号密码登录的请求，此时业务侧又不希望此类连接一直逗留在网关中，则可以用该指令去强制关闭它 |

### 业务进程请求网关，网关处理完毕再响应给业务进程的指令

| 名称                  | 编号                                                                                          | proto                                                                                                                                                                                                                         | 说明                                                                                 |
|---------------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------|
| 注册到网关               | [Register](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L51)         | [registerReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/registerReq.proto)、[registerResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/registerResp.proto)                                 | 业务进程向网关发起注册请求，检查注册条件后，会给连接异步写入注册成功的信息、将业务进程注册到管理器，让业务进程接收网关转发的客户数据，如果注册失败，会返回失败的信息 |
| 撤销注册                | [Unregister](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L52)       | -                                                                                                                                                                                                                             | 业务进程向网关发起取消注册请求，取消注册后，业务进程不会收到网关转发的客户数据                                            |
| 检查是否在线              | [CheckOnline](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L53)      | [checkOnlineReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/checkOnlineReq.proto)、[checkOnlineResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/checkOnlineResp.proto)                     | 检查某几个uniqId是否在网关中                                                                  |
| 获取全部连接的uniqId       | [UniqIdList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L54)       | [uniqIdListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/uniqIdListResp.proto)                                                                                                                           | -                                                                                  |
| 统计网关的在线连接数          | [UniqIdCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L55)      | [uniqIdCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/uniqIdCountResp.proto)                                                                                                                         | -                                                                                  |
| 统计网关的主题数量           | [TopicCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L56)       | [topicCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicCountResp.proto)                                                                                                                           | -                                                                                  |
| 获取网关的全部主题           | [TopicList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L57)        | [topicListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicListResp.proto)                                                                                                                             | -                                                                                  |
| 获取网关中某几个主题包含的uniqId | [TopicUniqIdList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L58)  | [topicUniqIdListReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdListReq.proto)、[topicUniqIdListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdListResp.proto)     | -                                                                                  |
| 统计网关中某几个主题包含的连接数    | [TopicUniqIdCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L59) | [topicUniqIdCountReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdCountReq.proto)、[topicUniqIdCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdCountResp.proto) | -                                                                                  |
| 获取连接的信息             | [ConnInfo](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L60)         | [connInfoReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connInfoReq.proto)、[connInfoResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/connInfoResp.proto)                                 | -                                                                                  |
| 获取网关状态的统计信息         | [Metrics](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L61)          | [metricsResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/metricsResp.proto)                                                                                                                                 | -                                                                                  |
| 设置并返回限流配置           | [Limit](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L62)            | [limitReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/limitReq.proto)、[limitResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/limitResp.proto)                                             | 先更新限流配置，如果传递的值无效，则会忽略它；再返回网关中的最新的限流配置                                              |
