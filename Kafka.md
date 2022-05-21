# Kafka

## 配置文件

```shell
# 当前机器在集群中的唯一标识，和zookeeper的myid性质一样
broker.id=0
host.name=192.168.90.2
# 这个是borker进行网络处理的线程数
num.network.threads=3
# 这个是borker进行I/O处理的线程数
num.io.threads=8
# 发送缓冲区buffer大小，数据不是一下子就发送的，先回存储到缓冲区了到达一定的大小后在发送，能提高性能
socket.send.buffer.bytes=102400
# kafka接收缓冲区大小，当数据到达一定大小后在序列化到磁盘
socket.receive.buffer.bytes=102400
# 这个参数是向kafka请求消息或者向kafka发送消息的请请求的最大数，这个值不能超过java的堆栈大小
socket.request.max.bytes=104857600
# 日志目录
log.dirs=/opt/app/kafka_2.11-0.10.1.1/logs
# 默认的分区数，一个topic默认1个分区数
num.partitions=1
num.recovery.threads.per.data.dir=1
# 默认消息的最大持久化时间，168小时，7天
log.retention.hours=168
# 因为kafka的消息是以追加的形式落地到文件，当超过这个值的时候，kafka会新起一个文件
log.segment.bytes=1073741824
# 每隔300000毫秒去检查上面配置的log失效时间
log.retention.check.interval.ms=300000
# 设置zookeeper的连接端口
zookeeper.connect=192.168.90.2:2181,192.168.90.3:2181,192.168.90.4:2181
# 设置zookeeper超时时间
zookeeper.connection.timeout.ms=6000
 
# 配置修改
# 添加以下内容：
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880
```



## 异常总结

- Kafka消费者启动时频繁打印日志

  ```shell
  #logback.xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
      <logger name="org.apache.kafka.clients" level="info" />
  </configuration>
  ```

- kafka整合项目启动，连接显示ip地址异常为hostname

  ```shell
  Connection to node 1 (HuangYiYi:9092) could not be established. Broker may not be available.
  
  #修改server.properties
  # 允许外部端口连接                                            
  listeners=PLAINTEXT://0.0.0.0:9092  
  # 外部代理地址                                                
  advertised.listeners=PLAINTEXT://150.158.169.154:9092
  ```

  

## 区别

### RabbitMQ

- RabbitMQ遵循AMQP协议
- RabbitMQ的broker由Exchange,Binding,queue组成，其中exchange和binding组成了消息的路由键
- 客户端Producer通过连接channel和server进行通信，Consumer从queue获取消息进行消费（长连接，queue有消息会推送到consumer端，consumer循环从输入流读取数据）
- rabbitMQ以broker为中心
- 有消息的确认机制。

## kafka组成部分

### Broker

- Kafka 集群包含一个或多个服务器，服务器节点称为broker
- broker存储topic的数据。如果某topic有N个partition，集群有N个broker，那么每个broker存储该topic的一个partition
- 如果某topic有N个partition，集群有(N+M)个broker，那么其中有N个broker存储该topic的一个partition，剩下的M个broker不存储该topic的partition数据
- 如果某topic有N个partition，集群中broker数目少于N个，那么一个broker存储该topic的一个或多个partition。在实际生产环境中，尽量避免这种情况的发生，这种情况容易导致Kafka集群数据不均衡。

### Producer

- 生产者即数据的发布者，该角色将消息发布到Kafka的topic中
- broker接收到生产者发送的消息后，broker将该消息到当前用于追加数据的segment文件中
- 生产者发送的消息，存储到一个partition中，生产者也可以指定数据存储的partition

### Partition

- topic中的数据分割为一个或多个partition
- 每个topic至少有一个partition
- 每个partition中的数据使用多个segment文件存储
- partition中的数据是有序的，不同partition间的数据丢失了数据的顺序。如果topic有多个partition，消费数据时就不能保证数据的顺序。在需要严格保证消息的消费顺序的场景下，需要将partition数目设为1

### Leader

- 每个partition有多个副本，其中有且仅有一个作为Leader，Leader是当前负责数据的读写的partition

### Follower

- Follower跟随Leader，所有写请求都通过Leader路由，数据变更会广播给所有Follower，Follower与Leader保持数据同步
- 如果Leader失效，则从Follower中选举出一个新的Leader
- 当Follower与Leader挂掉、卡住或者同步太慢，leader会把这个follower从“in sync replicas”（ISR）列表中删除，重新创建一个Follower

### Consumer

- 消费者可以从broker中读取数据。消费者可以消费多个topic中的数据

### Consumer Group

- 每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）
- 对于一个group而言，消费者的数量不应该多余分区的数量，因为在一个group中，每个分区至多只能绑定到一个消费者上，即一个消费者可以消费多个分区，一个分区只能给一个消费者消费
- 若一个group中的消费者数量大于分区数量的话，多余的消费者将不会收到任何消息。
