title: lsy
speaker: lsy
transition: move
theme: dark
files: /js/demo.js,/css/demo.css
date: 2017/1/19

[slide]

# flume的一些知识
## 演讲者：刘善永

[slide]

# 目录 {:&.flexbox.vleft}
## flume发展
## flume基本流程
## flume channel
## flume 部署
## flume设计优缺点

[slide]

# flume发展

[slide]

# 发展阶段 {:&.flexbox.vleft}
- flume og
- flume ng

# 产生原因
从各种数据源推送数据到hadoop生态系统的各种存储系统中

[slide]

# flume基本流程

[slide]

# 大体流程 {:&.flexbox.vleft}

![大体流程图](https://raw.githubusercontent.com/buptlsy/images/master/flume-liuchengtu.png)

[slide]

# 消息流转 {:&.flexbox.vleft}

![消息流转图](https://raw.githubusercontent.com/buptlsy/images/master/flume消息流转.png)

[slide]

# 消息格式 {:&.flexbox.vleft}

包括header和body。header放键值对，body放消息体，格式主要为两种：
### json格式
jackson序列化（不赋值的字段不会序列化）
### avro格式
#### 功能
- rpc调用
- 序列化反序列化  
a、定义schema b、可选择生成对应的类 c、序列化到内存或文件 d、从内存或者文件反序列化

#### 优点
- 占用空间少（不存key，优化了基本数据类型的空间）
- 序列化反序列化速度快

[slide]

# flume channel {:&.flexbox.vleft}

## channel selector
- 复制
- 多路选择
- 自定义

## channel 类型
- memory channel
- file channel

[slide]

# memory channel {:&.flexbox.vleft}

![memory channel事务图](https://raw.githubusercontent.com/buptlsy/images/master/flume-channel.png)

[slide]

# file channel {:&.flexbox.vleft}

![file channel图](https://raw.githubusercontent.com/buptlsy/images/master/file-channel1.png)
![file channel图](https://raw.githubusercontent.com/buptlsy/images/master/file-channel2.png)

[slide]

# flume source {:&.flexbox.vleft}

## 事件驱动
- HttpSource,AvroSource

## 轮询拉取
- JMSSource

[slide]

# flume sink {:&.flexbox.vleft}

## sink processor
- DefaultSinkProcessor
- FailoverSinkProcessor
- LoadBalanceSinkProcessor

[slide]

# flume 部署

[slide]

# 单层部署 {:&.flexbox.vleft}

# 双层部署
- 减少hdfs、hbase等介质的连接数
- 跨数据中心

![flume 部署图](https://raw.githubusercontent.com/buptlsy/images/master/Flumebushu.png)

[slide]

# flume 设计的优缺点

[slide]

# 优点 {:&.flexbox.vleft}
- 可扩展性很好，定制化开发，可以自定义source、channel、sink
- 可配置性非常强，每隔几秒重新加载配置

# 缺点
- 重复数据
- 监控太简陋，只能获取json串