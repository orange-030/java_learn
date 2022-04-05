# RocketMQ🚀

## 参考文档

👉  [RocketMQ 官方文档](http://rocketmq.apache.org/docs/quick-start/)

👉  [Github wiki 中文文档](https://github.com/apache/rocketmq/tree/master/docs/cn)





## 基本概念

### 消息模型 Message Model

RocketMQ 主要由 Producer、Broker、Consumer 三部分组成，其中 Producer 负责生产消息，Consumer 负责消费消息，Broker 负责存储消息。Broker 在实际部署过程中对应一台服务器，每个 Broker 可以存储多个 Topic 的消息，每个 Topic 的消息也可以分片存储于不同的 Broker。





### 消息 Message

消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。RocketMQ 中每个消息拥有唯一的Message ID，且可以携带具有业务标识的 Key。系统提供了通过 Message ID 和 Key 查询消息的功能。



### 消息队列 Message Queue

Message Queue 是用于存储消息的物理地址，用于并行发送和接收消息，相当于是 Topic 的分区，每个 Topic 中的消息地址存储于多个 Message Queue 中。



### 消息生产者 Producer

负责生产消息，一般由业务系统负责生产消息。一个消息生产者会把业务应用系统里产生的消息发送到 broker 服务器。RocketMQ 提供多种发送方式，同步发送、异步发送、顺序发送、单向发送。同步和异步方式均需要 Broker 返回确认信息，单向发送不需要。



### 消息消费者 Consumer

负责消费消息，一般是后台系统负责异步消费。一个消息消费者会从 Broker 服务器拉取消息、并将其提供给应用程序。从用户应用的角度而言提供了两种消费形式：拉取式消费、推动式消费。



### 生产者组 Producer Group 

同一类 Producer 的集合，这类 Producer 发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则 Broker 服务器会联系同一生产者组的其他生产者实例以提交或回溯消费。



### 消费者组 Consumer Group 

消费组是同一类 Consumer 的集合，这类 Consumer 通常消费同一类消息且消费逻辑一致。消费者组使得在消息消费方面，实现集群和容错的目标变得非常容易。要注意的是，**消费者组的消费者实例必须订阅完全相同的 Topic 。**

RocketMQ 支持两种消息模式：集群消费（Clustering）和广播消费（Broadcasting）。

如果同一个 GroupID 下的不同消费者实例，订阅了不同的 Topic 和 Tag ，将导致集群模式下在对 Topic 的消费产生不正确的结果，最终导致消息丢失。



### 主题 Topic

Topic 表示一类消息的集合，每个主题包含若干条消息，每条消息只能属于一个主题，是 RocketMQ 进行消息订阅的基本单位。

一个Topic可能有 0 个，一个或多个生产者向它发送消息，相反，一个生产者可以发送不同类型 Topic 的消息。类似的，消费者组可以订阅一个或多个主题，只要该组的实例保持其订阅一致即可。



### 标签 Tag

Tag 是消息设置的标志，用于在同一 Topic 下区分不同类型的消息。来自同一业务单元的消息，可以根据不同业务目的在同一 Topic 下设置不同 Tag。 Tag 能够有效地保持代码的清晰度和连贯性，并优化 RocketMQ 提供的查询系统。

消费者可以根据 Tag 实现对不同子主题的不同消费逻辑，实现更好的扩展性。



### 代理服务器 Broker Server

Broker Server 是消息中转角色，负责存储消息、转发消息。代理服务器在 RocketMQ 系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备。

Broker Server 也存储消息相关的元数据，包括消费者组、消费进度偏移、主题和队列消息等。



### 名字服务 Name Server

NameServer 充当路由消息的提供者，其角色类似 Dubbo 中的 Zookeeper。生产者或消费者能够通过 NameServer 查找各主题相应的 Broker IP 列表。

多个 Name Server 实例组成集群，但相互独立，没有信息交换，Broker是向每一台 NameServer 注册自己的路由信息，所以每一个NameServer 实例上面都保存一份完整的路由信息。当某个 NameServer 因某种原因下线了，Broker 仍然可以向其它 NameServer同步其路由信息，Producer、Consumer 仍然可以动态感知 Broker 的路由的信息。







# QuickStart🚀

## 构建项目

解压文件并用 maven 构建项目

```sh
unzip rocketmq-all-4.8.0-source-release.zip
cd rocketmq-all-4.8.0/
mvn -Prelease-all -DskipTests clean install -U
cd distribution/target/rocketmq-4.8.0/rocketmq-4.8.0
```





## 修改启动参数

```shell
vim bin/runserver.sh

JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"


vim bin/runbroker.sh
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"


vim conf/broker.conf
brokerIP1 = 106.75.81.25  # 如果有公网IP且需要暴露，则配置此项
```

另外，在 `runserver.sh` 中要注释这一行，否则在 JDK 1.8 下启动会报错

```
JAVA_OPT="${JAVA_OPT} -Xlog:gc*:file=${GC_LOG_DIR}/rmq_srv_gc_%p_%t.log:time,tags:filecount=5,filesize=30M"
```





## 启动项目

```sh
# 启动 nameserver
nohup sh bin/mqnamesrv &
nohup ./bin/mqnamesrv -n 120.79.198.81:9876 &
# 查看是否正常
tail -f ~/logs/rocketmqlogs/namesrv.log

# 启动broker
nohup sh bin/mqbroker -n 127.0.0.1:9876 &
nohup sh bin/mqbroker -n 120.79.198.81:9876 -c conf/broker.conf autoCreateTopicEnable=true &
# 查看是否正常
tail -f ~/logs/rocketmqlogs/broker.log 
```



如果 rocketMQ-4.8.0 启动时 broker.log  有如下错误，则手动创建文件夹

```
2021-05-08 12:20:22 ERROR DiskCheckScheduledThread1 - Error when measuring disk space usage, file doesn't exist on this path: /home/ubuntu/store/commitlog
2021-05-08 12:20:50 ERROR StoreScheduledThread1 - Error when measuring disk space usage, file doesn't exist on this path: /home/ubuntu/store/consumequeue
```

```sh
mkdir -p ~/store/commitlog
mkdir -p ~/store/consumequeue
```





## 测试

```sh
export NAMESRV_ADDR=localhost:9876
export NAMESRV_ADDR=120.79.198.81:9876

# 发送消息 
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer

# 接收消息
sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer
```



## 关闭项目

```sh
# 1.关闭NameServer
sh bin/mqshutdown namesrv

# 2.关闭Broker
sh bin/mqshutdown broker
```









# RocketMQ特性🚀

## 消息顺序

消息有序指的是一类消息消费时，能按照发送的顺序来消费。例如：一个订单产生了三条消息分别是订单创建、订单付款、订单完成。消费时要按照这个顺序消费才能有意义，但是同时订单之间是可以并行消费的。RocketMQ 可以严格的保证消息有序。

顺序消息分为全局顺序消息与分区顺序消息，全局顺序是指某个 Topic 下的所有消息都要保证顺序；分区顺序消息只要保证每一组消息被顺序消费即可。

- 全局顺序对于指定的一个 Topic，所有消息按照严格的先入先出（FIFO）的顺序进行发布和消费。 如果是全局顺序，则代表该 Topic 下只有一个分片。

  适用场景：性能要求不高，所有的消息严格按照 FIFO 原则进行消息发布和消费的场景

  <img src="http://store.secretcamp.cn/uPic/image-20210825212230662202108252122301629897750IQxtlxIQxtlx.png" alt="image-20210825212230662" style="zoom:40%;" />

  

- 分区顺序对于指定的一个 Topic，所有消息根据 sharding key 进行区块分区。 同一个分区内的消息按照严格的 FIFO 顺序进行发布和消费。 Sharding key 是顺序消息中用来区分不同分区的关键字段，和普通消息的 Key 是完全不同的概念。 

  适用场景：性能要求高，以 sharding key 作为分区字段，在同一个区块中严格的按照 FIFO 原则进行消息发布和消费的场景。

  <img src="http://store.secretcamp.cn/uPic/image-20210825212300119202108252123001629897780BUJExDBUJExD.png" alt="image-20210825212300119" style="zoom:40%;" />



## 消息重试

RocketMQ 提供了一种重试机制，在 Consumer 消费消息失败后，令消息再消费一次。

Consumer 消费消息失败通常可以认为有以下几种情况：

- 由于消息本身的原因，例如反序列化失败，消息数据本身无法处理（例如话费充值，当前消息的手机号被注销，无法充值）等。这种错误通常需要跳过这条消息，再消费其它消息，而这条失败的消息即使立刻重试消费，99% 也不成功，所以最好提供一种定时重试机制，例如过 10 秒后再重试。
- 由于依赖的下游应用服务不可用，例如 db 连接不可用，外系统网络不可达等。遇到这种错误，即使跳过当前失败的消息，消费其他消息同样也会报错。这种情况建议应用 sleep 30s，再消费下一条消息，这样可以减轻 Broker 重试消息的压力。

RocketMQ 会为每个消费组都设置一个 Topic 名称为 `%RETRY%+consumerGroup` 的重试队列，用于暂时保存因为各种异常而导致Consumer 端无法消费的消息，这里需要注意的是，这个 Topic 的重试队列是针对消费组，而不是针对每个 Topic 设置的。

考虑到异常恢复起来需要一些时间，会为重试队列设置多个重试级别，每个重试级别都有与之对应的重新投递延时，重试次数越多投递延时就越大。RocketMQ 对于重试消息的处理是先保存至 Topic 名称为 `SCHEDULE_TOPIC_XXXX` 的延迟队列中，后台定时任务按照对应的时间进行 Delay 后重新保存至 `%RETRY%+consumerGroup` 的重试队列中。

```
messageDelayLevel=1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h
```



## 死信队列

死信队列（Dead-Letter Queue）用于处理无法被正常消费的消息。当一条消息初次消费失败，消息队列会自动进行消息重试，达到最大重试次数后，若消费依然失败，则表明消费者在正常情况下无法正确地消费该消息，此时，消息队列 不会立刻将消息丢弃，而是将其发送到该消费者对应的特殊队列中。

这种消息称为死信消息（Dead-Letter Message），在 RocketMQ 中，可以通过使用 console 控制台对死信队列中的消息进行重发来使得消费者实例再次进行消费。



## 消费幂等

在互联网应用中，尤其在网络不稳定的情况下，消息队列 RocketMQ 的消息有可能会出现重复，有以下几种情况：

- 生产时消息重复

  当一条消息已被成功发送到服务端并完成持久化，此时出现了网络闪断或者客户端宕机，导致服务端对客户端应答失败。 如果此时生产者意识到消息发送失败并尝试再次发送消息，消费者后续会收到两条内容相同并且 Message ID 也相同的消息。

- 消费时消息重复

  消息消费的场景下，消息已投递到消费者并完成业务处理，当客户端给服务端反馈应答的时候网络闪断。为了保证消息至少被消费一次，消息队列 RocketMQ 的服务端将在网络恢复后再次尝试投递之前已被处理过的消息，消费者后续会收到两条内容相同并且 Message ID 也相同的消息。

- 集群模式下消息重复

  当 Broker 或客户端重启、扩容或缩容时，会触发 Rebalance，此时消费者可能会收到重复消息。



因为 Message ID 有可能出现冲突（重复）的情况，所以真正安全的幂等处理，不建议以 Message ID 作为处理依据。 最好的方式是以业务唯一标识作为幂等处理的关键依据，而业务的唯一标识可以通过消息 Key 进行设置：

```java
Message message = new Message();
message.setKey("ORDERID_100");
SendResult sendResult = producer.send(message);
```

消费者收到消息时可以根据消息的 Key 进行幂等处理：

```java
consumer.subscribe("ons_test", "*", new MessageListener() {
    public Action consume(Message message, ConsumeContext context) {
        String key = message.getKey()
        // 根据业务唯一标识的 key 做幂等处理
    }
});
```

也可以自定义唯一流转 handleId 放在消息体中，反序列化后再根据这个handleId 实现幂等。



# 集群🚀

## 集群架构

### NameServer

NameServer是一个非常简单的 Topic 路由注册中心，其角色类似 Dubbo 中的 Zookeeper，支持 Broker 的动态注册与发现。它是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。

主要包括两个功能：

- Broker 管理

  NameServer 接受 Broker 集群的注册信息并且保存下来作为路由信息的基本数据，然后提供心跳检测机制，检查 Broker 是否还存活。

- 路由信息管理

  每个 NameServer 将保存关于 Broker 集群的整个路由信息和用于客户端查询的队列信息。然后 Producer 和 Conumser 通过 NameServer 就可以知道整个 Broker 集群的路由信息，从而进行消息的投递和消费。NameServer 通常也是集群的方式部署，各实例间相互不进行信息通讯。Broker 是向每一台 NameServer 注册自己的路由信息，所以每一个 NameServer 实例上面都保存一份完整的路由信息。当某个 NameServer 因某种原因下线了，Broker 仍然可以向其它 NameServer 同步其路由信息，Producer、Consumer 仍然可以动态感知 Broker 的路由的信息。



### BrokerServer

Broker Server 主要负责消息的存储、投递和查询以及服务高可用保证。

Broker 分为 Master 与 Slave，一个 Master 可以对应多个 Slave，但是一个 Slave 只能对应一个 Master，Master 与 Slave 的对应关系通过指定相同的 BrokerName，不同的 BrokerId 来定义，BrokerId 为 0 表示 Master，非 0 表示 Slave。

Master 也可以部署多个，每个 Broker 与 NameServer 集群中的所有节点建立长连接，定时注册 Topic 信息到所有 NameServer。



### Producer

消息发布的角色，支持分布式集群方式部署。Producer 通过 MQ 的负载均衡模块选择相应的 Broker 集群队列进行消息投递，投递的过程支持快速失败并且低延迟。

Producer 与 NameServer 集群中的其中一个节点（随机选择）建立长连接，定期从 NameServer 获取 Topic 路由信息，并向提供 Topic 服务的 Master 建立长连接，且定时向 Master 发送心跳。Producer 完全无状态，可集群部署。



### Consumer

消息消费的角色，支持分布式集群方式部署。支持以 push、pull 两种模式对消息进行消费。同时也支持集群方式和广播方式的消费，它提供实时消息订阅机制，可以满足大多数用户的需求。

Consumer 与 NameServer 集群中的其中一个节点（随机选择）建立长连接，定期从 NameServer 取 Topic 路由信息，并向提供 Topic 服务的 Master、Slave 建立长连接，且定时向 Master、Slave 发送心跳。

Consumer 既可以从 Master 订阅消息，也可以从 Slave 订阅消息，Master服务器会根据拉取偏移量与最大偏移量的距离（防止重复消费），以及从服务器是否可读等因素建议下一次是从 Master 还是 Slave 拉取。



<img src="http://store.secretcamp.cn/uPic/image-20210424113922852202104241139231619235563YMeZZ4YMeZZ4.png" alt="image-20210424113922852" style="zoom:40%;" />





## 主从模式

Master 角色的 Broker 支持读和写，Slave 角色的 Broker 仅支持读，也就是 Producer 只能和 Master 角色的 Broker 连接写入消息； Consumer 可以连接 Master 角色的 Broker，也可以连接 Slave 角色的 Broker 来读取消息。

1. 消息生产的高可用机制

   在创建 Topic 的时候，把 Topic 的多个 Message Queue 创建在多个 Broker 组上（相同Broker名称，不同 brokerId的一组机器），这样当一个 Broker 组的 Master 不可用后，其他组的 Master 仍然可用，Producer 仍然可以发送消息。

    RocketMQ 目前还不支持把 Slave 自动转成 Master，如果机器资源不足， 需要把 Slave 转成 Master，则要手动停止 Slave 角色的 Broker，更改配置文 件，用新的配置文件启动 Broker。

2. 消息消费的高可用机制

   在 Consumer 的配置文件中，并不需要设置是从 Master 读还是从 Slave 读，当 Master 不可用或者繁忙的时候，Consumer 会被自动切换到从 Slave 读。有了自动切换 Consumer 这种机制，当一个 Master 角色的机器出现故障后，Consumer 仍然可以从Slave 读取消息，不影响 Consumer 程序。这就达到了消费端的高可用性。





## 主从复制

### 同步复制

同步复制方式是等 Master 和 Slave 均写成功后才反馈给客户端写成功状态；

在同步复制方式下，如果 Master 出故障， Slave 上有全部的备份数据，容易恢复，但是同步复制会增大数据写入 延迟，降低系统吞吐量。

### 异步复制 

异步复制方式是只要 Master 写成功即可反馈给客户端写成功状态。

在异步复制方式下，系统拥有较低的延迟和较高的吞吐量，但是如果 Master 出了故障，有些数据因为没有被写入 Slave，有可能会丢失；





## 集群模式

### 单主节点

这种方式风险较大，一旦 Broker 重启或者宕机时，会导致整个服务不可用。不建议线上环境使用,可以用于本地测试。



### 多主节点

一个集群无 Slave，全是 Master，例如 2 个 Master 或者 3 个 Master，这种模式的优缺点如下：

- 优点：配置简单，单个 Master 宕机或重启维护对应用无影响，在磁盘配置为 RAID10 时，即使机器宕机不可恢复情况下，由于 RAID10 磁盘非常可靠，消息也不会丢（异步刷盘丢失少量消息，同步刷盘一条不丢），性能最高；
- 缺点：单台机器宕机期间，这台机器上未被消费的消息在机器恢复之前不可订阅，消息实时性会受到影响。



### 多主多从（异步）

每个 Master 配置一个 Slave，有多对 Master-Slave，主从复制采用异步复制方式，主备有短暂消息延迟（毫秒级），这种模式的优缺点如下：

- 优点：即使磁盘损坏，消息丢失的非常少，且消息实时性不会受影响，同时 Master 宕机后，消费者仍然可以从 Slave 消费，而且此过程对应用透明，不需要人工干预，性能同多 Master 模式几乎一样；
- 缺点：Master 宕机，磁盘损坏情况下会丢失少量消息。



### 多主多从（同步）

每个 Master 配置一个Slave，有多对 Master-Slave，主从复制采用同步方式，即只有主备都写成功，才向应用返回成功，这种模式的优缺点如下：

- 优点：数据与服务都无单点故障，Master 宕机情况下，消息无延迟，服务可用性与数据可用性都非常高；
- 缺点：性能比异步复制模式略低（大约低10%左右），发送单个消息的 RT 会略高，且目前版本在主节点宕机后，备机不能自动切换为主机。



## 两主两从集群Sample

### 防火墙配置

* nameserver 默认使用 9876 端口
* broker-master 默认使用 10911 端口
* broker-slave 默认使用 11011 端口



### 配置文件

在 `$ROCKETMQ_HOME/conf` 路径下，已经为用户准备好了一些配置文件

<img src="http://store.secretcamp.cn/uPic/image-20210508141204437202105081412041620454324uFhBnwuFhBnw.png" alt="image-20210508141204437" style="zoom: 50%;" />



两个 Name Server，Broker Server 两主两从，异步方式

```
10.10.10.1 namesrv1
10.10.10.2 namesrv2
10.10.10.3 broker-a
10.10.10.4 broker-b
10.10.10.5 broker-a-s
10.10.10.6 broker-b-s
```



各项配置的详细介绍：

```sh
# 所属集群的名字
brokerClusterName=rocketmq-cluster
# broker的名字，不同的 broker-group 对应的名称不同
brokerName=broker-a
# 0 表示 Master，>0 表示 Slave
brokerId=0
# 当前 broker 监听的IP
brokerIP1=10.10.10.3
# broker 主从模式下，在 broker主节点上配置了 brokerIP2 的话，broker 从节点会连接主节点配置的 brokerIP2 来同步。
# 如果当前 broker 是 slave，则不需要配置配置brokerIP2
brokerIP2=10.10.10.3
# nameServer地址，分号分隔。
namesrvAddr=10.10.10.1:9876;10.10.10.2:9876
# Broker 对外服务的监听端口   slave -> 11011
listenPort=10911

# 存储路径
storePathRootDir=/usr/local/rocketmq/store
# commitLog 存储路径
storePathCommitLog=/usr/local/rocketmq/store/commitlog
# 消费队列存储路径存储路径
storePathConsumeQueue=/usr/local/rocketmq/store/consumequeue
# 消息索引存储路径
storePathIndex=/usr/local/rocketmq/store/index
# checkpoint 文件存储路径
storeCheckpoint=/usr/local/rocketmq/store/checkpoint
# abort 文件存储路径
abortFile=/usr/local/rocketmq/store/abort

#限制的消息大小
maxMessageSize=65536

# Broker 的角色
#   如果是MASTER： ASYNC_MASTER 异步复制 Master
#                  SYNC_MASTER  同步双写 Master
#   如果是SLAVE：    SLAVE 
brokerRole=SYNC_MASTER


# 刷盘方式
#    ASYNC_FLUSH 异步刷盘
#    SYNC_FLUSH 同步刷盘
flushDiskType=SYNC_FLUSH

# 在发送消息时，自动创建服务器不存在的topic，默认创建的队列数
defaultTopicQueueNums=4
# 是否允许 Broker 自动创建Topic，建议线下开启，线上关闭
autoCreateTopicEnable=true
# 是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭
autoCreateSubscriptionGroup=true
# 删除文件时间点，默认凌晨 4点
deleteWhen=04
# 文件保留时间，默认 48 小时
fileReservedTime=120
# commitLog每个文件的大小默认1G
mapedFileSizeCommitLog=1073741824
# ConsumeQueue每个文件默认存30W条，根据业务情况调整
mapedFileSizeConsumeQueue=300000
```





### 启动集群

1. 启动 nameServer 集群

   ```sh
   nohup sh bin/mqbroker &
   ```

2. 启动 broker 集群

   根据不同的配置文件启动 broker

   ```sh
   nohup sh bin/mqbroker -c conf/broker-{?}.conf
   ```






# 集群监控🚀

## mqadmin





## rocketmq-console

github：https://github.com/apache/rocketmq-externals/tree/master/rocketmq-console



1. 修改配置

在  `src/main/recource`  下修改  `application.properties` 的配置

```sh
rocketmq.config.namesrvAddr=106.75.81.25:9876
```



2. 启动项目

```sh
mvn spring-boot:run
```

或者

```sh
mvn clean package -Dmaven.test.skip=true
cd target
java -jar rocketmq-console-ng-1.0.1.jar.jar
```



# 消息生产🚀

## 基本概念

### 普通顺序消息 

普通顺序消息（Normal Ordered Message）模式下，消费者通过同一个消费队列收到的消息是有顺序的，不同消息队列收到的消息则可能是无顺序的。



### 严格顺序消息

严格顺序消息（Strictly Ordered Message）模式下，消费者收到的所有消息均是有顺序的。



## 基本消息样例

### 发送流程

首先引入 rocketmq-client 的 maven 依赖

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.8.0</version>
</dependency>
```



1. 消息发送者步骤分析

```tex
1.创建消息生产者producer，并制定生产者组名
2.指定Nameserver地址
3.启动producer
4.创建消息对象，指定主题Topic、Tag和消息体
5.发送消息
6.关闭生产者producer
```



2. 消息消费者步骤分析

```tex
1.创建消费者Consumer，制定消费者组名
2.指定Nameserver地址
3.订阅主题Topic和Tag
4.设置回调函数，处理消息
5.启动消费者consumer
```





### 发送消息

#### 同步消息

这种可靠性同步地发送方式使用的比较广泛，比如：重要的消息通知，短信通知。

```java
public class SyncProducer {
    public static void main(String[] args) throws Exception {
        // 实例化消息生产者 Producer
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        // 设置NameServer的地址
        producer.setNamesrvAddr("106.75.81.25:9876");
        // 启动Producer实例
        producer.start();
        for (int i = 0; i < 20; i++) {
            String content = "同步消息" + i;
            // 创建消息，并指定Topic，Tag和消息体
            Message msg = new Message("base", "tag1", content.getBytes());
          
            // 发送消息到一个Broker，并接受发送的结果
            SendResult result = producer.send(msg);
            SendStatus status = result.getSendStatus();
            String id = result.getMsgId();
            int queueId = result.getMessageQueue().getQueueId();
            System.out.println("状态： " + status);
            System.out.println("id： " + id);
            System.out.println("队列id： " + queueId);
            TimeUnit.SECONDS.sleep(1);
        }
        // 关闭 Producer 实例。
        producer.shutdown();
    }
}
```



#### 异步消息

异步消息通常用在对响应时间敏感的业务场景，即发送端不能容忍长时间地等待 Broker 的响应。

```java
public class AsyncProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("106.75.81.25:9876");
        producer.start();
        for (int i = 0; i < 10; i++) {
            // topic  tags  contents
            String content = "异步消息" + i;
            Message msg = new Message("base", "tag2", content.getBytes());
            final int tmp = i;
            producer.send(msg, new SendCallback() {
                // 发送成功的回调函数
                public void onSuccess(SendResult sendResult) {
                    System.out.println("第" + tmp + "条发送成功： " + sendResult + " 当前线程：" + Thread.currentThread().getName());
                }

                // 发送失败的回调函数
                public void onException(Throwable throwable) {
                    System.out.println("第" + tmp + "发送异常： " + throwable);

                }
            });
            System.out.println("第" + i + "条已经发完");
            TimeUnit.MILLISECONDS.sleep(50);
        }
        producer.shutdown();
    }
}
```



#### 单向消息

这种方式主要用在不特别关心发送结果的场景，例如日志发送。

```java
public class OneWayProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("106.75.81.25:9876");
        producer.start();
        for (int i = 0; i < 10; i++) {
            // topic  tags  contents
            String content = "单向消息" + i;
            Message msg = new Message("base", "tag3", content.getBytes());
            producer.sendOneway(msg);
            TimeUnit.MILLISECONDS.sleep(100);
        }
        producer.shutdown();
    }
}
```



## 顺序消息

消息有序指的是可以按照消息的发送顺序来消费（FIFO）。RocketMQ 可以严格的保证消息有序，可以分为「分区有序」或者「全局有序」。

顺序消费的原理解析，在默认的情况下消息发送会采取轮询方式把消息发送到不同的 queue（分区队列）；而消费消息的时候从多个 queue 上拉取消息，这种情况发送和消费是不能保证顺序。但是如果控制发送的顺序消息只依次发送到同一个 queue 中，消费的时候只从这个 queue 上依次拉取，则就保证了顺序。当发送和消费参与的 queue 只有一个，则是全局有序；如果多个 queue 参与，则为分区有序，即相对每个 queue ，消息都是有序的。

用订单进行分区有序的示例：一个订单的顺序流程是：创建、付款、推送、完成。订单号相同的消息会被先后发送到同一个队列中，消费时，同一个 OrderId 获取到的肯定是同一个队列。

### 顺序消息生产

```java
public class OrderProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("106.75.81.25:9876");
        producer.start();
        List<OrderStep> orders = OrderStep.buildOrders();
        int count = 0;
        for (OrderStep order : orders) {
            String keys = "count" + count++;
            String body = order.toString() + "";
            Message msg = new Message("order", "orderTag", keys, body.getBytes());
            SendResult result = producer.send(msg, new MessageQueueSelector() {
                /**
                 * @param mqs 队列集合
                 * @param message 消息对象
                 * @param o 业务标识的参数
                 * @return
                 */
                public MessageQueue select(List<MessageQueue> mqs, Message message, Object o) {
                    long orderId = Long.parseLong(String.valueOf(o));
                    long index = orderId % mqs.size();
                    return mqs.get((int) index);
                }
            }, order.getOrderId());
            System.out.println(result);
        }
        producer.shutdown();
    }
}
```



### 顺序消息消费

```java
public class OrderConsumer {
    public static void main(String[] args) throws Exception{
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("106.75.81.25:9876");
        consumer.subscribe("order", "orderTag");
        consumer.registerMessageListener(new MessageListenerOrderly() {
            @Override
            public ConsumeOrderlyStatus consumeMessage(List<MessageExt> msgs, ConsumeOrderlyContext consumeOrderlyContext) {
                for (MessageExt msg : msgs) {
                    System.out.println(new String(msg.getBody()) + " - " + Thread.currentThread().getName());
                }
                return ConsumeOrderlyStatus.SUCCESS;
            }
        });
        consumer.start();
    }
}
```



## 延时消息

### 延迟等级

rocketMQ 并不支持任意时间的延时，需要设置几个固定的延时等级，从1s到2h分别对应着等级1到18

```java
// org/apache/rocketmq/store/config/MessageStoreConfig.java

private String messageDelayLevel = "1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h";
```



### 发送延时消息

```java
public class DelayProducer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("106.75.81.25:9876");
        producer.start();
        for (int i = 0; i < 20; i++) {
            String content = "同步消息" + i;
            Message msg = new Message("base", "tag-delay", content.getBytes());
            msg.setDelayTimeLevel(2);
            SendResult result = producer.send(msg);
            SendStatus status = result.getSendStatus();
            String id = result.getMsgId();
            int queueId = result.getMessageQueue().getQueueId();
            System.out.println("状态： " + status);
            System.out.println("id： " + id);
            System.out.println("队列id： " + queueId);
            TimeUnit.MILLISECONDS.sleep(100);
        }
        producer.shutdown();
    }
}
```



## 批量消息

### 消息发送

```java
public class BatchProducer {
    public static void main(String[] args) throws Exception{
        // 实例化消息生产者 Producer
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        // 设置 NameServer 的地址
        producer.setNamesrvAddr("106.75.81.25:9876");
        // 启动 Producer 实例
        producer.start();
        List<Message> msgs = new ArrayList<>();
        Message msg1 = new Message("base", "batch", "Hello World 1".getBytes());
        Message msg2 = new Message("base", "batch", "Hello World 2".getBytes());
        Message msg3 = new Message("base", "batch", "Hello World 3".getBytes());
        msgs.add(msg1);
        msgs.add(msg2);
        msgs.add(msg3);
        SendResult result = producer.send(msgs);
        System.out.println(result);
        // 关闭 Producer 实例。
        producer.shutdown();
    }
}
```



### 消息分割

如果消息的总长度可能大于 4MB 时，这时候需要把消息进行分割



## 过滤消息

### SQL基本语法

RocketMQ 只定义了一些基本语法来支持这个特性。你也可以很容易地扩展它。

* 数值比较，比如：>，>=，<，<=，BETWEEN，=；
* 字符比较，比如：=，<>，IN；
* IS NULL 或者 IS NOT NULL；
* 逻辑符号 AND，OR，NOT；

常量支持类型为：

* 数值，比如：123，3.1415；
* 字符，比如：'abc'，必须用单引号包裹起来；
* NULL，特殊的常量
* 布尔值，TRUE 或 FALSE

只有使用 push 模式的消费者才能用使用 SQL92 标准的 sql 语句，接口如下：

```java
public void subscribe(finalString topic, final MessageSelector messageSelector)
```



### 消息生产

```java
public class FilterProduct {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("group1");
        producer.setNamesrvAddr("106.75.81.25:9876");
        producer.start();
        for (int i = 0; i < 20; i++) {
            String content = "同步消息" + i;
            Message msg = new Message("base", "tag", content.getBytes());
          
            // 为消息添加属性 key - value
            msg.putUserProperty("flag", String.valueOf(i));

            SendResult result = producer.send(msg);
            SendStatus status = result.getSendStatus();
            String id = result.getMsgId();
            int queueId = result.getMessageQueue().getQueueId();
            System.out.println("状态： " + status);
            System.out.println("id： " + id);
            System.out.println("队列id： " + queueId);
            TimeUnit.MILLISECONDS.sleep(100);
        }
        producer.shutdown();
    }
}
```





### 消息消费

```java
public class FilterConsumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        // 指定 nameServer
        consumer.setNamesrvAddr("106.75.81.25:9876");
        // 订阅 topic ，此时不订阅 tag，而是通过 sql 过滤
        consumer.subscribe("base", MessageSelector.bySql("flag>10"));
        // 注册消息监听器
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            // 接收消息内容
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext consumeConcurrentlyContext) {
                for (MessageExt msg : list) {
                    System.out.println(new String(msg.getBody()) + " - " + Thread.currentThread().getName());
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者
        consumer.start();

    }
}
```





## 事务消息

### 流程分析

![image-20210509202535719](http://store.secretcamp.cn/uPic/image-20210509202535719202105092025361620563136UxJskiUxJski.png)

上图说明了事务消息的大致方案，其中分为两个流程：正常事务消息的发送及提交、事务消息的补偿流程。



#### 事务消息发送及提交

1. 发送消息（half消息）。

2. 服务端响应消息写入结果。
3. 根据发送结果执行本地事务（如果写入失败，此时half消息对业务不可见，本地逻辑不执行）。

4. 根据本地事务状态执行 Commit 或者 Rollback（Commit操作生成消息索引，消息对消费者可见）



#### 事务补偿

补偿阶段用于解决消息 Commit 或者 Rollback 发生超时或者失败的情况。

1. 对没有 Commit/Rollback 的事务消息（pending状态的消息），从服务端发起一次 "回查"
2. Producer 收到回查消息，检查回查消息对应的本地事务的状态

3. 根据本地事务状态，重新 Commit 或者 Rollback



#### 事务消息状态

事务消息共有三种状态，提交状态、回滚状态、中间状态：

* `TransactionStatus.CommitTransaction`：提交事务，它允许消费者消费此消息。
* `TransactionStatus.RollbackTransaction`：回滚事务，它代表该消息将被删除，不允许被消费。
* `TransactionStatus.Unknown`：中间状态，它代表需要检查消息队列来确定状态。



### 消息生产

```java
public class TransactionProducer {

    public static void main(String[] args) throws Exception {
        // 实例化消息生产者 Producer
        TransactionMQProducer producer = new TransactionMQProducer("group1");

        // 设置NameServer的地址
        producer.setNamesrvAddr("106.75.81.25:9876");

        // 添加事务的监听器
        producer.setTransactionListener(new TransactionListener() {
            @Override
            public LocalTransactionState executeLocalTransaction(Message msg, Object o) {
                System.out.println("执行本地事务" + "->" + msg.getTags());
                if (StringUtils.equals("TagA", msg.getTags())) {
                    return LocalTransactionState.COMMIT_MESSAGE;
                } else if (StringUtils.equals("TagB", msg.getTags())) {
                    return LocalTransactionState.ROLLBACK_MESSAGE;
                } else if (StringUtils.equals("TagC", msg.getTags())) {
                    return LocalTransactionState.UNKNOW;
                }
                return LocalTransactionState.UNKNOW;
            }

            @Override
            public LocalTransactionState checkLocalTransaction(MessageExt msg) {
                System.out.println("MQ检查消息Tag" + msg.getTags() + "的本地事务执行结果");
                return LocalTransactionState.COMMIT_MESSAGE;
            }
        });

        // 启动 Producer
        producer.start();
        String[] tags = {"TagA", "TagB", "TagC"};
        for (int i = 0; i < 3; i++) {
            String context = "TRANSACTION" + i;
            Message msg = new Message("transaction", tags[i], context.getBytes());
            SendResult result = producer.sendMessageInTransaction(msg, null);
            System.out.println(result);
            TimeUnit.MILLISECONDS.sleep(100);
        }

        // 关闭 Producer 实例。
        producer.shutdown();
    }


}

```





### 使用限制

1. 事务消息不支持延时消息和批量消息。
2. 为了避免单个消息被检查太多次而导致半队列消息累积，我们默认将单个消息的检查次数限制为 15 次，但是用户可以通过 Broker 配置文件的 `transactionCheckMax` 参数来修改此限制。如果已经检查某条消息超过 N 次， Broker 将丢弃此消息，并在默认情况下同时打印错误日志。用户可以通过重写 `AbstractTransactionCheckListener` 类来修改这个行为。
3. 事务消息将在 Broker 配置文件中的参数 `transactionMsgTimeout` 这样的特定时间长度之后被检查。当发送事务消息时，用户还可以通过设置用户属性 `CHECK_IMMUNITY_TIME_IN_SECONDS` 来改变这个限制，该参数优先于 `transactionMsgTimeout` 参数。
4. 事务性消息可能不止一次被检查或消费，这就要求消费方做好幂等性检查。
5. 提交给用户的目标主题消息可能会失败，目前这依日志的记录而定。它的高可用性通过 RocketMQ 本身的高可用性机制来保证，如果希望确保事务消息不丢失、并且事务完整性得到保证，建议使用同步的双重写入机制。
6. 事务消息的生产者 ID 不能与其他类型消息的生产者 ID 共享。与其他类型的消息不同，事务消息允许反向查询、MQ 服务器能通过它们的生产者 ID 查询到消费者。





# 消息消费🚀

## 基本概念

### 拉取式消费 Pull Consumer

Consumer 消费的一种类型，应用通常主动调用 Consumer 的拉消息方法从 Broker 服务器拉消息、主动权由应用控制。一旦获取了批量消息，应用就会启动消费过程。



### 推送式消费 Push Consumer

Consumer 消费的一种类型，该模式下 Broker 收到数据后会主动推送给消费端，该消费模式一般实时性较高。



### 集群消费 Clustering

集群消费模式下，相同 Consumer Group 的每个 Consumer 实例平均分摊消息。



### 广播消费 Broadcasting

广播消费模式下，相同 Consumer Group 的每个 Consumer 实例都接收全量的消息。







## 消费模式

Consumer 端的两种消费模式（Push/Pull）都是基于 Pull 模式来获取消息的，而在 Push 模式只是对 Pull 模式的一种封装，其本质实现为消息拉取线程在从服务器拉取到一批消息后，然后提交到消息消费线程池后，又立刻向服务器再次尝试拉取消息。



###  push模式

如果要定义多个消费者，则要指定不同的 group。对于一个 group，同一个应用只能有一个消费者

```java
@Service
@RocketMQMessageListener(topic = "spring-boot-rocketmq",
        consumeMode = ConsumeMode.ORDERLY,
        consumerGroup = "group1")
public class DemoConsumer1 implements RocketMQListener<String> {
    @Override
    public void onMessage(String s) {
        System.out.println("Consumer01接收到消息...");
        System.out.println(s);
    }
}

@Service
@RocketMQMessageListener(topic = "spring-boot-rocketmq",
        consumeMode = ConsumeMode.ORDERLY,
        consumerGroup = "group2")
public class DemoConsumer2 implements RocketMQListener<String> {
    @Override
    public void onMessage(String s) {
        System.out.println("Consumer02接收到消息...");
        System.out.println(s);
    }
}
```





### pull模式







## 集群模式

消费者采用集群方式消费消息，多个消费者共同消费队列消息，每个消费者处理的消息不同

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("106.75.81.25:9876");
      	// 如果要订阅所有 tag ，则传递 "*"
        consumer.subscribe("base", "tag3");
        // 使用集群模式（默认）
        consumer.setMessageModel(MessageModel.CLUSTERING);
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            // 接收消息内容 注册回调函数
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext consumeConcurrentlyContext) {
                for (MessageExt msg : list) {
                    System.out.println(new String(msg.getBody()) + " - " + Thread.currentThread().getName());
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
    }
}
```



## 广播模式

消费者采用广播的方式消费消息，每个消费者消费的消息都是相同的

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        consumer.setNamesrvAddr("106.75.81.25:9876");
        consumer.subscribe("base", "tag3");
        // 使用广播模式
        consumer.setMessageModel(MessageModel.BROADCASTING);
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            // 接收消息内容 注册回调函数
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext consumeConcurrentlyContext) {
                for (MessageExt msg : list) {
                    System.out.println(new String(msg.getBody()) + " - " + Thread.currentThread().getName());
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        consumer.start();
    }
}
```



## 消费者负载均衡

Consumer 端负载均衡的核心设计理念是在一个消息消费队列在同一时间只允许被同一消费组内的一个消费者消费，一个消息消费者能同时消费多个消息队列。







# 消息持久化🚀

RocketMQ 是一款高性能、高可靠的分布式消息中间件。

通常高性能和高可靠是很难兼得的。因为要保证高可靠，那么数据就必须持久化到磁盘上，将数据持久化到磁盘，那么可能就不能保证高性能了，RocketMQ 在兼容这两方面做的十分不错。



## 消息存储「顺序写」

RocketMQ 是通过顺序写来保证消息的高效存储。

顺序写速度可以达到 1500 MB/s，超过了一般网卡的传输速度。但是如果不进行人工干预，磁盘采用的是随机写，在随机写的情况下，磁盘的写入速度急速下降，磁盘的随机写速度可能只有几百 KB/s，这远远要慢于网络传输速度。

RocketMQ 在持久化的设计上，采取的是消息顺序写、随机读的策略，利用磁盘顺序写的速度，让磁盘的写速度不会成为系统的瓶颈。



## 消息发送「零拷贝」

将磁盘中的消息发送出去，要经过 4 次拷贝

1. 从磁盘复制数据到内核态内存；
2. 从内核态内存复制到用户态内存；
3. 然后从用户态内存复制到网络驱动的内核态内存；
4. 最后是从网络驱动的内核态内存复制到网卡中进行传输。

零拷贝（Zero-copy）技术是指计算机执行操作时，CPU 不需要先将数据从某处内存复制到另一个特定区域。这种技术通常用于通过网络传输文件时节省 CPU 周期和内存带宽。

RocketMQ 通过 `MappedByteBuffer` 对文件进行读写，利用了 NIO 中的 `FileChannel` 模型将磁盘上的物理文件直接映射到用户态的内存地址中，实现了 “零拷贝”，即省去了上述第 2 步，直接将磁盘数据从内核态复制到网络驱动的内核态。

正因为需要使用 MMAP 内存映射机制，故 RocketMQ 的文件存储都使用定长结构来存储，方便一次将整个文件映射至内存。



## 消息存储结构

RocketMQ 消息的存储是由 ConsumeQueue 和 CommitLog 配合完成的。

在 RocketMQ 持久化机制中，涉及到了三个角色：



### CommitLog

CommitLog 是消息真正的存储文件，所有消息都存储在 CommitLog 文件中。

单个文件大小默认1G，文件名长度为 20 位，左边补零，剩余为起始偏移量，比如 00000000000000000000 代表了第一个文件，起始偏移量为 0，文件大小为 1G=1073741824 。当第一个文件写满了，第二个文件为 00000000001073741824 ，起始偏移量为1073741824 ，以此类推。



### ConsumeQueue

ConsumeQueue 是消息消费逻辑队列，类似数据库的索引文件，存储的是指向物理存储的地址，每个 Topic 下的每个 Message Queue 都有一个对应的 ConsumeQueue 文件。

ConsumeQueue 作为消费消息的索引，保存了指定 Topic 下的队列消息在 CommitLog 中的起始物理偏移量 offset 、消息大小 size和消息 Tag 的 hashCode 。



### IndexFile

- IndexFile：消息索引文件，提供了一种可以通过 key 或时间区间来查询消息的方法。





## 存储过程

生产者将消息发送到 RocketMQ 的 Broker 后，Broker 服务器会将消息顺序写入到 CommitLog 文件中，磁盘顺序写特别快，RocketMQ 充分利用了这一点，极大的提高消息写入效率。

但是消费者消费消息的时候，可能就会遇到麻烦，每一个消费者只能订阅一个 Topic ，消费者关心的是该 Topic 下的所有消息，但是同一主题的消息在 CommitLog 文件中可能是不连续的，那么消费者消费消息的时候，需要将 CommitLog 文件加载到内存中遍历查找订阅主题下的消息，频繁的 I/O 操作，性能就会急速下降。

为了解决这个问题，RocketMQ 引入了 ConsumeQueue 文件。ConsumeQueue 文件类似于 MySQL 索引文件，不会存储消息的全量信息，而是存放了同一主题下的所有消息的地址，消费者消费的时候只需要去对应的 ConsumeQueue 组中取消息即可。Consumequeue 文件，这样做可以带来以下两个好处：

- ConsumeQueue 文件内容小，可以尽可能的保证 ConsumeQueue 文件全部读入到内存，提高消费效率。
- ConsumeQueue 文件也是会持久化的，不存全量信息可以节约磁盘空间。

<img src="http://store.secretcamp.cn/uPic/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAyMC80LzgvMTcxNTkxOGYwMDcwY2E5NA202105262012341622031154M4xMddM4xMdd.png"  />

## 刷盘机制

RocketMQ 的消息是存储到磁盘上的，这样既能保证断电后恢复，又可以让存储的消息量超出内存的限制。

RocketMQ 为了提高性能，会尽可能地保证磁盘的顺序写。消息在通过 Producer 写入 RocketMQ 的时候，有两种写磁盘方式，同步刷盘和异步刷盘。

PageCache 是 OS 对文件的缓存，用于加速对文件的读写，所以一般都是先写入到 PageCache 中，然后再持久化到磁盘上。MySQL、Redis 等都是如此，RocketMQ 也不列外。



### 同步刷盘

在返回写成功状态时，消息已经被写入磁盘。具体流程是，消息写入内存的 PageCache 后，立刻通知刷盘线程刷盘， 然后等待刷盘完成，刷盘线程执行完成后唤醒等待的线程，返回消息写成功的状态。

这种方式可以保证数据绝对安全，但是吞吐量不大。



### 异步刷盘

在返回写成功状态时，消息可能只是被写入了内存的 PageCache（页缓存），写操作的返回快，吞吐量大；当内存里的消息量积累到一定程度时，统一触发写磁盘动作，快速写入。这种方式吞吐量大，性能高，但是 PageCache 中的数据可能丢失，不能保证数据绝对的安全。

PageCache 是 OS 对文件的缓存，用于加速对文件的读写，程序对文件进行顺序读写的速度几乎接近于内存的读写速度，主要原因就是由于 OS 使用 PageCache 机制对读写访问操作进行了性能优化，将一部分的内存用作 PageCache 。

- 对于数据的写入，OS 会先写入至 PageCache 内，随后通过异步的方式由 pdflush内核线程将缓存内的数据刷盘至物理磁盘上。

- 对于数据的读取，如果一次读取文件时出现未命中 PageCache 的情况，OS 从物理磁盘上访问读取文件的同时，会顺序对其他相邻块的数据文件进行预读取。



<img src="http://store.secretcamp.cn/uPic/image-20210526162437759202105261624381622017478jegfjVjegfjV.png" alt="image-20210526162437759" style="zoom:50%;" />







# RocketMQ原理🚀

## Topic

对于RocketMQ ，一个 Topic 可以分布在各个 Broker 上，把一个 Topic 分布在一个 Broker 上的子集定义为一个 Topic 分片。

每个Topic 分片还可以分为若干等分，其中的一份就是一个 ConsumerQueue，每一个 ConsumerQueue 代表了这个 Topic 的部分数据，每个 Topic 分片的 Queue 数量可以不同，由用户在创建 Topic 时指定。

这样就可以做到每个 Topic 的数据都可以分布式存储在集群中的多台 Broker 上，形成了分布式的存储架构，这样就可以利用多台机器的资源来存储数据。

同时，可以利用多台机器的资源来应对高并发的请求，因为对一个 Topic 的读写请求，就会均匀的落到多台 Broker 机器上去了。



<img src="http://store.secretcamp.cn/uPic/image-20210825220340189202108252203401629900220cPOqJ3cPOqJ3.png" alt="image-20210825220340189" style="zoom: 33%;" />





## Producer

每个 Producer 采用发送给 Topic 的某个 MessageQueue

主要有三类发送方式：

1. 一种是直接发消息，Producer 内部有选择 Queue 的算法，不允许外界改变。
2. 内置算法
   - `SelectMessageQueueByRandom` ：纯随机
   - `SelectMessageQueueByHash` ：纯取余
   - `SelectMessageQueueByMachineRoom` ：没用
3. 用户自定义算法

<img src="http://store.secretcamp.cn/uPic/image-20210825222246886202108252222471629901367XIDZIJXIDZIJ.png" alt="image-20210825222246886" style="zoom:50%;" />





## Consumer

### 集群模式

集群模式下每个消费者实例也会被平均分配到多个 MessageQueue ，这些 MessageQueue 可能位于不同的分片。

如果要做到顺序消息，就必须把消息确保投递到同一个 MessageQueue 。

<img src="http://store.secretcamp.cn/uPic/image-20210825222411842202108252224121629901452svmwJ8svmwJ8.png" alt="image-20210825222411842" style="zoom: 33%;" />



例如，生产者可以对订单号做取模预算，有相同订单号 -> 有相同的模--->分发到相同的 MessageQueue

<img src="http://store.secretcamp.cn/uPic/image-20210825223256729202108252232561629901976EFEeYWEFEeYW.png" alt="image-20210825223256729" style="zoom:43%;" />



### 广播模式

广播模式下，每个 Consumer 负责订阅的 Topic 下的所有 MessageQueue 。



### Consumer的数量

Consumer 的数量必须小于 MessageQueue 的个数，这是由 RocketMQ 的负载均衡算法决定的。

- Queue > Consumer，且 Queue能整除 Consumer， 那么 Consumer 会平均分配 Queue
- Queue > Consumer，且 Queue不能整除 Consumer， 那么会有一个Consumer多消费 1 个 Queue，其余 Consumer平均分配。
- Queue < Consumer，那么会有 Consumer 闲置，就是浪费了，其余 Consumer 平均分配到 Queue上。





## Broker

RocketMQ 的消息的存储形式：ConsumeQueue + CommitLog

- CommitLog：消息真正的物理存储文件，每台 Broker上的 CommitLog 被本机器所有 ConsumeQueue 共享。

- ConsumeQueue：消息的逻辑队列，类似数据库的索引文件，存储的是指向物理存储的地址。每个 Topic 下的每个 MessageQueue 都有一个对应的 ConsumeQueue 文件。



在 CommitLog 中，一个消息的存储长度是不固定的，RocketMQ 采取一些机制，尽量向 CommitLog 中顺序写，但是随机读，每次读取消息队列先读取 ConsumerQueue，然后再通过 ConsumerQueue 去 CommitLog 中拿到消息主体。





# RocketMQ-Springboot🚀

👉  [Github](https://github.com/apache/rocketmq-spring)

👉  [Github API wiki](https://github.com/apache/rocketmq-spring/wiki/%E7%94%A8%E6%88%B7%E6%89%8B%E5%86%8C)



## 配置文件

```properties
# application.properties

# nameServer
rocketmq.name-server=106.75.81.25:9876
rocketmq.producer.group=my-group
```

1. 生产者

   ```properties
   # 必须配置项
   # 多个nameServer 可用 ; 分隔
   rocketmq.nameServer=106.75.81.25:9876
   rocketmq.producer.group=my-group
   
   # 可选配置项
   rocketmq.producer.send-message-timeout=300000
   rocketmq.producer.compress-message-body-threshold=4096
   rocketmq.producer.max-message-size=4194304
   rocketmq.producer.retry-times-when-send-async-failed=0
   rocketmq.producer.retry-next-server=true
   rocketmq.producer.retry-times-when-send-failed=2
   ```



2. 消费者

   ```properties
   # 必须配置项
   # 作为 @RocketMQMessageListener 中 consumerGroup 和 nameServer 缺省参数
   # 如果在 @RocketMQMessageListener 具体指定了，这里可以不配置
   rocketmq.name-server=120.79.198.81:9876
   rocketmq.consumer.group=my-group
   
   # 可选配置项
   ```



## RocketMQTemplate

`RocketMQTemplate` 是 Springboot 为 RocketMQ 提供的模板类，发送各种消息更方便，提供了许多重载的方法发送各种消息。





## 消息生产

- `destination`：格式为 `topicName:tagName`

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ProducerApplication.class)
public class ProducerTest {
    @Autowired
    RocketMQTemplate rocketMQTemplate;

    @Autowired
    ApplicationContext ac;

    @Test
    public void testAC() {
        System.out.println(">>>>>>>>>>>>>>>>>>>>>>");
        for (String bean : ac.getBeanDefinitionNames()) {
            System.out.println(bean);
        }
    }

    @Test
    public void sendMessageSynchronously() {
        rocketMQTemplate.convertAndSend("spring-boot-rocketmq", "convertAndSend");
    }

    @Test
    public void sendSpringMessage() {
        Message<String> message = MessageBuilder.withPayload("Hello, World! I'm from spring message").build();
        rocketMQTemplate.send("spring-boot-rocketmq", message);
    }

    @Test
    public void sendMessageAsync() {
        User user = new User(1, "潘子");
        rocketMQTemplate.asyncSend("spring-boot-rocketmq-xs", user, new SendCallback() {
            @Override
            public void onSuccess(SendResult sendResult) {
                System.out.printf("%n%n%n发送成功 SendResult=%s %n%n%n", sendResult);
            }
            @Override
            public void onException(Throwable throwable) {
                System.out.printf("%n%n%n发送失败 Throwable=%s %n%n%n", throwable);
            }
        });
        System.out.println("发送完成...");
    }

    @Test
    public void sendOrderMessage() {
        Message<String> message = MessageBuilder.withPayload("I'm from spring message").build();
        rocketMQTemplate.syncSendOrderly("spring-boot-rocketmq", message, "hashKey");
    }
}
```



## 消息消费



### RocketMQListener

消费者需要继承 `RocketMQListener` 接口，再用 `@RocketMQMessageListene` 定义相关属性，最后将该组件注入容器

```java
@Service
@RocketMQMessageListener(topic = "spring-boot-rocketmq",
        consumeMode = ConsumeMode.ORDERLY)
public class DemoConsumer implements RocketMQListener<String> {
    @Override
    public void onMessage(String s) {
        System.out.println("接收到消息...");
        System.out.println(s);
    }
}
```



如果要获取消息的原生属性，只需要将泛型设置为 `MessageExt` 即可。

```java
@Service
@RocketMQMessageListener(topic = "spring-boot-rocketmq", consumerGroup = "my-consumer_test-topic-1")
public class DemoConsumer implements RocketMQListener<MessageExt>{
    public void onMessage(MessageExt messageExt) {
        System.out.println("接收到消息...");
        System.out.println(messageExt);        
    }
}
```







# FAQ🚀

## 怎么保证消息不丢失？

消息可能丢失的场景：

1. 生产阶段丢失

   在生产阶段 Producer 将消息通过网络发送给 Broker ，当 Broker 收到消息后给 Producer 一个 ACK ，如果 Producer 没有收到正确的响应，Producer 直到收到 Broker 的确认响应后才会停止重试消息发送。

2. 磁盘丢失

   Broker收到来自Producer的消息，持久化到磁盘，如果是异步刷盘，消息一开始只被写入到内存的 PageCache ，消息量积累到一定程度时才会触发写磁盘动作，如果写入磁盘之前 Broker 挂了，可能会丢消息。

   可以通过同步刷盘或异步刷盘 + 主从架构保证消息不丢失

3. 消费阶段丢失

   一定要当消费者处理完自身业务逻辑后给 Broker 发送消费确认，否则收到消息就给 Broker 确认消费，此时Broker认为消费者消费成功，将消息从Broker队列中移除，万一消费失败了，那消息就丢失了也没法重试。





## 消息积压怎么办？

消息积压要么是生产者消息数量增加导致的积压，要么就是消费者消费变慢导致的消息积压，所以一旦出现消息积压的问题，要么是线上流量徒增，要么就是业务逻辑异常。

- 生产端：检查业务逻辑是否有异常的耗时步骤导致的
- Broker 端：检查消息队列内存使用情况、每个分区积压的消息数量差异，如果分区消息数量均匀，可以认为是流量激增，需要在消费端做优化，或者扩容（增加 Broker 节点）。如果分区消息数量差异很大，则要检查路由转发规则是否合理。

- 消费端

  一个 Comsumer 可以消费多个 MessageQueue ，Comsumer 数量固定时，可以增加 Broker 上的 MessageQueue 数量，加快消费速度。但是如果  Consumer 数量已经大于 MessageQueue 时，MessageQueue 数量不变，且仅增加消费者是没有用处的，因为多个消费者在同一个分区上实际是单线程资源竞争关系。





## RocketMQ消息何时删除？







## 消费失败怎么办？

消费成功：更新消费者本地的 offsetStore ，即消费进度加一

消费失败：消费失败的节点会回退给 Broker 节点，这些消息会进入 Retry Topic，每个消费者组都有一个专属的 Retry Topic， 即 `%Group%Retry Topic` ，消费者在启动时都会订阅这个 Topic。

但是消费失败的消息，不会立刻进入 Retry Topic，因为立刻去消费还很有可能失败，这不是一个好的策略。

RocketMQ 将 Retry Topic 和队列保存到消息属性中，修改消息的 Topic 为 `SCHEDULE_TOPIC_XXXX` ，XXXX 由消息重试的次数决定，重试次数越多，XXXX 越大，延时也就越高。

再由 Broker 服务负责消费  `SCHEDULE_TOPIC_XXXX` 中的消息，如果消费的消息满足了延迟时间，就会成功消费消息，将这个消息的 Topic 改为 Retry Topic 。



## RocketMQ读写分离？

每次 Consumer 到 Broker 去拉消息，Broker 都会根据消息结果集中的最后一条消息，来推荐消费者下一次是到 Master 还是到 Slave 去拉取下一批消息。

如果消息结果集最后一条消息是热数据，那么下ー次拉消息请求会到 Master，如果最后一条消息是冷数据，那下一次拉取消息就推荐到 Slave 

- 热数据：消费者消费速度大于生产速度；
- 冷数据：消费速度低于生产速度，消息堆积，距离 CommitLog 最大的 offset 大小超过系统内存的 40%







## RocketMQ怎么实现顺序写？

对于顺序写的 CommitLog 文件，Broker 会锁定它对应内存的映射空间，不允许它释放内存，同时在写之前会进行预热，避免出现缺页异常。

被写满的 CommitLog 文件对应的内存映射缓冲区会解除锁定限制，允许换回外存，一般是使用 LRU，因为是顺序写，所以会退化成 FIFO，即最早的内存也会被先释放。

