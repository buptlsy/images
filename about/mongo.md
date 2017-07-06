title: lsy
speaker: lsy
transition: move
theme: dark
highlightStyle: monokai_sublime
files: /js/demo.js,/css/demo.css
date: 2017/7/1

[slide]

# mongodb 的那些事儿

[slide]

# 目录 {:&.flexbox.vleft}

## mongodb简介
## mongodb的基本操作
## 副本集和分片
## mongodb读写策略
## 其他的一些点

[slide]

# mongodb 简介 {:&.flexbox.vleft}

mongodb是一个高性能，开源，无模式且易于扩展的通用型数据库。使用c++开发。目前由10gen公司维护。

### 特点

#### 易于使用
```
- 面向文档的数据库，能仅使用一条记录来表现复杂的层级关系
- 没有固定模式，根据需要添加或删除字段变得更容易
```

```
{
   _id: ObjectId("5099803df3f4948bd2f98391"),
   name: { first: "Alan", last: "Turing" },
   birth: new Date('Jun 23, 1912'),
   death: new Date('Jun 07, 1954'),
   contribs: [ "Turing machine", "Turing test", "Turingery" ],
   views : NumberLong(1250000)
}
```

[slide]

# mongodb 简介 {:&.flexbox.vleft}

###### 丰富的功能
```
- 索引(唯一索引，复合索引，地理空间索引以及全文索引)
- 聚合(MapReduce、count、group、distinct等)
- 文件存储(GridFS 存储大文件和文件元数据)
- 特殊的集合类型(时间有限的集合、固定大小的集合)
```

###### 易于扩展
```
- 采用横向扩展。能自动处理集群的数据和负载，自动重新分配文档，以及将用户请求路由到正确的机器上
```
###### 高性能
```
- 将热数据放在内存中(索引和预读取数据)，加快读写速度
```

[slide]

# mongodb 简介 {:&.flexbox.vleft}

###### mongodb vs mysql

 |mongo| mysql
|:-------|:------
|db-Collection-Document |　db-table-row
|不支持事务 | innodb引擎支持事务
|动态模式 | 固定模式
|副本集+分片实现高可用 | M-S,MHA,MMM,cluster架构方式



[slide]

# mongodb 基本操作 {:&.flexbox.vleft}

mongodb在保留json基本key/value特性的基础上，添加了其他一些数据类型。

```
- 布尔型(true、false)
- 数值 {"x":3}  {"x":3.14}
- 字符串 {"x":"foobar"}
- 日期 {"x": new Date()}
- 数组 {"x":[a, b, c]}
- 内嵌文档 {"x":{"foo":"bar"}}
- 对象id {"x":ObjectId()}
- 二进制数据
```


[slide] 

# mongodb 基本操作 {:&.flexbox.vleft}

###### 插入文档
```
- db.foo.insert({"foo":"bar"})
- db.foo.batchInsert([{"_id":0}, {"_id":1}, {"_id":2}])
```

###### 删除文档
```
- db.foo.remove()
- db.foo.remove({"opt":true})
- db.foo.drop()
删除数据是永久性的，不能撤销也不能恢复
```

###### 查询
```
- find
- $lt, $lte, $gt, $gte
- $in, $or, $nin
- $skip, $limit, $sort
```

[slide]

# mongodb 基础知识 {:&.flexbox.vleft}

###### 更新文档
```
- $set   db.user.update({"name":"jode"}, {"$set":{"book":"hha"}})
- $unset db.user.update({"name":"jode"}, {"$set":{"book":"hha"}})
- $inc   db.user.update({"name":"jode"}, {"$inc":{"age":1}})
- $push  db.post.update({"title":"lsy"}, {"$push":{"comments":{"name":"test", "content":"welcome"}}})
- $pop  {"$pop":{"key":1}} 从数组末尾删除
- $pull 删除所有匹配的文档
- upsert 
  db.analytics.update({"url":"/blog"}, {"$inc":{"pageview":1}}, true) 操作原子性
```

[slide]

# mongodb 基础知识 {:&.flexbox.vleft}

##### 索引

###### 唯一索引(unique)
```
- _id
- 会把null看做值，无法把多个缺少唯一索引的键的文档插入集合
- 索引字段大小限制为1kb
```

###### 稀疏索引(sparse)
```
- 不需要将每个文档都作为索引条目
```

###### 复合索引
```
{ userid: 1, score: -1 }
```

###### TTL索引
```
- 允许为每个文档设置一个超时时间。达到预设值就会被删除。  expireAfterSecs
- mongodb每分钟对ttl索引进行一次清理
```

[slide]

# mongodb 副本集、分片

[slide]

# mongodb 副本集  {:&.flexbox.vleft}

![副本集 最小构成图](https://raw.githubusercontent.com/buptlsy/images/gh-pages/replica-set-primary-with-two-secondaries.png)

###### 仲裁者(arbiter)
```
- 最避免出现平票。
- 不存储数据。不知道应该将一个成员作为数据节点还是仲裁者时，应该将其作为数据节点
```

[slide]

# mongodb 副本集  {:&.flexbox.vleft}

###### 优先级
```
- 渴望成为主节点的程度。0~100。0表示永远不会成为主节点（被动成员）
- priority
- 成为主节点，必须是拥有最新的数据
```

###### 隐藏成员
```
- hidden
- 客户端不会向成员发送请求
```

###### 延迟备份节点
```
- slaveDelay
- priority=0,不接受读请求
- 比主节点延迟指定的时间
```

[slide]

# mongodb 副本集  {:&.flexbox.vleft}

###### 同步
```
- oplog是主节点local数据库中的一个固定的集合，包含了主节点的每一次写操作
```

###### 心跳
```
- 每隔2s向其他成员发送一次心跳请求，检查每个成员的状态
- 让主节点知道自己是否满足集合大多数的条件
```

###### 选举
```
- 当一个成员节点不能到达主节点时，会申请成为主节点。达到大多数条件，就会成为主节点
```

[slide]

# mongodb 分片

[slide]

![mongo 分片](https://raw.githubusercontent.com/buptlsy/images/gh-pages/sharded-cluster-production-architecture.png)

###### 配置服务器 
```
- 保存着集群和分片的元数据，即各分片包含哪些数据的信息。
- 不需要太多的空间和资源。1kb的空间约等于200mb的真实数据
```

[slide]

# mongodb 分片 {:&.flexbox.vleft}

##### chunk 块
```
每个chunk由给定片键特定范围内的文档组成。一个块只存在于一个分片上。
```

###### 拆分chunk
```
- mongos记录每个chunk中插入了多少数据，如果达到拆分阙值点。mongos更新配置服务器上chunk的元信息。
  块拆分只改变元数据，不进行数据移动。
- mongos进程重新启动，计数会重新开始
```

##### balancer 均衡器
```
- 负责数据迁移，周期性检查分片间是否存在不平衡
- mongos 变身为均衡器
```

[slide]

# mongodb 分片 {:&.flexbox.vleft}

### 片键
片键决定了集群中一个集合的 documents 在不同 shards 中的分布.片键字段必须被索引,且在集合中的每条记录都不能为空

#### 基于范围的片键
![mongo 升序片键](https://raw.githubusercontent.com/buptlsy/images/gh-pages/sharding-range-based.png)

```
- 所有的写请求都会路由到一个分片中
- 数据均衡处理变得困难
- 适合做范围查询
```

[slide]

# mongodb 分片 {:&.flexbox.vleft}

### 片键

#### 随机分发的片键
![mongo 随机分发片键](https://raw.githubusercontent.com/buptlsy/images/gh-pages/sharding-hash-based.png)

```
- 数据平均分布，插入更有效率
- 无法做范围查询
```

[slide]

# mongdb副本集 + 分片

![mongo 集群部署](https://raw.githubusercontent.com/buptlsy/images/gh-pages/mongo-bushutu.png)

[slide]

# mongodb读写策略

[slide]

# mongodb 写入安全 {:&.flexbox.vleft}

###### 写入安全
```
- 应答式写入  数据库会给出响应，告诉操作是否成功执行。db.getLastError();
- 非应答式写入
- 等待写入复制 db.runCommand({"getLastError":1, "w":"majoity", "wtimeout":1000})
```

![mongo write concern](https://raw.githubusercontent.com/buptlsy/images/gh-pages/write-concern_1.png)

[slide]

# mongodb 写入安全 {:&.flexbox.vleft}

### 写入提交
```
- 日志系统(journaling).每次写入会建立一条日志(mongodb默认每隔100ms或者写入数据达到若干兆字节
	时，将这些操作写入日记).数据文件默认没60s刷新到磁盘，因此日志文件只需记录60s的写入数据
- j选项确定写入操作刷盘
  db.foo.insert({"x":1})  db.runCommand({"getLastError":1, "j":true})
  提交一次写入操作会同时提交这之前的所有写操作
```

[slide]

# mongodb读策略  {:&.flexbox.vleft}

###### readPreference
```
- primary 只从 primary 节点读数据，这个是默认设置
- primaryPreferred  优先从 primary 读取，primary 不可服务，从 secondary 读
- secondary  只从 scondary 节点读数据
- secondaryPreferred 优先从 secondary 读取，没有 secondary 成员时，从 primary 读取
- nearest 根据网络距离就近读取
```

###### readConcern
```
- 解决脏读问题
- local 能读取任意数据，这个是默认设置
- majority 只能读取到 成功写入到大多数节点的数据
- 无论哪种级别的readConcern,客户端都只会从某一个确定的节点(readPreference)读取数据，
  该节点根据自己看到的同步状态视图，只会返回已经同步到大多数节点的数据
- 使用 readConcern 需要配置replication.enableMajorityReadConcern选项
- 只有支持 readCommited 隔离级别的存储引擎才能支持 readConcern，比如 wiredtiger 引擎，
  而 mmapv1引擎则不能支持。
```

[slide]

# mongodb的其他特性 {:&.flexbox.vleft}

###### 固定集合
```
- 事先创建的大小固定的集合
- 类似于循环队列，当满时会将老数据删除
- db.createCollection("my_collection":{"capped":true, "size":100000, "max":100})
- 可进行自然排序，返回的结果集文档的顺序就是文档在磁盘上的顺序
```

###### 循环游标(tailable cursor)
``` 
- 由于普通集合不维护文档的插入顺序，只能用在固定集合上
- 10分钟没有新的结果，会被释放
```

[slide]

![mongo ring buffer](https://raw.githubusercontent.com/buptlsy/images/gh-pages/ring_buffer.png)

[slide]

# id生成 {:&.flexbox.vleft}

![mongo objectId](https://raw.githubusercontent.com/buptlsy/images/gh-pages/objectId.png)

[slide]

# mongodb的存储引擎 {:&.flexbox.vleft}

###### mmap
```
- memory mapped file
- db lock, collection lock
- 分配内存 power of 2 byte sizes、精确匹配、预分配
- 引起碎片
- 不能压缩数据
```

###### wiredtiger
```
- document lock
- 支持压缩
- 可以手动调节存储引擎的参数(cache size, checkpoint interval, logging)
```


[slide]

###### 推荐一些网址和入门书籍
```
- 官网  http://docs.mongoing.com/
- learn mongodb the hard way  http://learnmongodbthehardway.com/
- mongodb driver  http://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/
- nosqlfan  http://blog.nosqlfan.com/
- mongodb权威指南
```

[slide]

# Q&A