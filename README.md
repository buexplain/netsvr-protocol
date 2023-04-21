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

| 名称                 | 编号                                                                                           | proto                                                                                                     | 说明                                                                                |
|--------------------|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|
| 更新连接的信息            | [InfoUpdate](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L36)        | [infoUpdate.proto](https://github.com/buexplain/netsvr-protocol/blob/main/infoUpdate.proto)               | 每个客户连接都可以在网关中存储：唯一id、主题标签、session信息，该指令用于设置这些信息                                   |
| 删除连接的信息            | [InfoDelete](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L37)        | [infoDelete.proto](https://github.com/buexplain/netsvr-protocol/blob/main/infoDelete.proto)               | 删除客户连接的：唯一id、主题标签、session信息                                                       |
| 广播                 | [Broadcast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L38)         | [broadcast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/broadcast.proto)                 | 给网关中所有客户连接都发送一条信息                                                                 |
| 组播                 | [Multicast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L39)         | [multicast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/multicast.proto)                 | 给指定的某几个客户连接发送一条信息                                                                 |
| 单播                 | [SingleCast](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L40)        | [singleCast.proto](https://github.com/buexplain/netsvr-protocol/blob/main/singleCast.proto)               | 给指定的某个客户连接发送一条信息                                                                  |
| 订阅主题               | [TopicSubscribe](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L41)    | [topicSubscribe.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicSubscribe.proto)       | 给客户连接打上一些标签，比如客户加入的群的id                                                           |
| 取消订阅的主题            | [TopicUnsubscribe](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L42)  | [topicUnsubscribe.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUnsubscribe.proto)   | 删除客户连接的标签，比如客户退群了，可以将之前标记的群id删除                                                   |
| 删除主题               | [TopicDelete](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L43)       | [topicDelete.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicDelete.proto)             | 从有此标签的客户连接身上删除该标签                                                                 |
| 根据主题发布消息           | [TopicPublish](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L44)      | [topicPublish.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicPublish.proto)           | 根据标签找到所有含此标签的客户连接，并将信息发送给它们                                                       |
| 强制关闭某个连接           | [ForceOffline](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L45)      | [forceOffline.proto](https://github.com/buexplain/netsvr-protocol/blob/main/forceOffline.proto)           | 强制关闭某个客户连接，比如客户在多个设备连接到网关，则可用此指令去强制关闭客户其它设备的连接                                    |
| 强制关闭某个空session值的连接 | [ForceOfflineGuest](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L46) | [forceOfflineGuest.proto](https://github.com/buexplain/netsvr-protocol/blob/main/forceOfflineGuest.proto) | 强制关闭某个空session值的连接，比如客户连接到网关，但是并没有发起账号密码登录的请求，此时业务侧又不希望此类连接一直逗留在网关中，则可以用该指令去强制关闭它 |

### 业务进程请求网关，网关处理完毕再响应给业务进程的指令

| 名称                 | 编号                                                                                          | proto                                                                                                                                                                                                                         | 说明                                      |
|--------------------|---------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
| 注册到网关              | [Register](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L50)         | [register.proto](https://github.com/buexplain/netsvr-protocol/blob/main/register.proto)                                                                                                                                       | 业务进程向网关发起注册请求，注册后，业务进程才会收到网关转发的客户数据     |
| 撤销注册               | [Unregister](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L51)       | -                                                                                                                                                                                                                             | 业务进程向网关发起取消注册请求，取消注册后，业务进程不会收到网关转发的客户数据 |
| 检查是否在线             | [CheckOnline](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L52)      | [checkOnlineReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/checkOnlineReq.proto)、[checkOnlineResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/checkOnlineResp.proto)                     | 检查某几个uniqId是否在网关                        |
| 获取全部连接的uniqId      | [UniqIdList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L53)       | [uniqIdListReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/checkOnlineReq.proto)、[uniqIdListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/uniqIdListResp.proto)                        | -                                       |
| 统计网关的在线连接数         | [UniqIdCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L54)      | [uniqIdCountReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/uniqIdCountReq.proto)、[uniqIdCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/uniqIdCountResp.proto)                     | -                                       |
| 统计网关的主题数量          | [TopicCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L55)       | [topicCountReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicCountReq.proto)、[topicCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicCountResp.proto)                         | -                                       |
| 获取网关的全部主题          | [TopicList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L54)        | [topicListReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicListReq.proto)、[topicListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicListResp.proto)                             | -                                       |
| 获取网关中某个主题包含的uniqId | [TopicUniqIdList](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L57)  | [topicUniqIdListReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdListReq.proto)、[topicUniqIdListResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdListResp.proto)     | -                                       |
| 统计网关中某个主题包含的连接数    | [TopicUniqIdCount](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L58) | [topicUniqIdCountReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdCountReq.proto)、[topicUniqIdCountResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/topicUniqIdCountResp.proto) | -                                       |
| 获取连接的信息            | [Info](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L58)             | [infoReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/infoReq.proto)、[infoResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/infoResp.proto)                                                 | -                                       |
| 获取网关状态的统计信息        | [Metrics](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L60)          | [metricsReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/metricsReq.proto)、[metricsResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/metricsResp.proto)                                     | -                                       |
| 设置限流配置             | [Limit](https://github.com/buexplain/netsvr-protocol/blob/main/router.proto#L61)            | [limitReq.proto](https://github.com/buexplain/netsvr-protocol/blob/main/limitReq.proto)、[limitResp.proto](https://github.com/buexplain/netsvr-protocol/blob/main/limitResp.proto)                                             | 更新限流配置，并返回网关中的限流配置的真实情况                 |
