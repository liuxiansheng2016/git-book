# mongoDB

NoSQL 可以大体上分为 4 个种类：\
**Key-value、Document-Oriented、Column-Family Databases 以及 Graph-Oriented Databases**

| **类型**                               | **代表**     | **特点**                                                                |
| ------------------------------------ | ---------- | --------------------------------------------------------------------- |
| 键值（Key-Value）                        | MemcacheDB | 键值数据库就像在传统语言中使用的哈希表。你可以通过 key 来添加、查询或者删除数据，鉴于使用主键访问，所以会获得不错的性能及扩展性。   |
| 面向文档（Document-Oriented）              | MongoDB    | 文档存储一般用类似 json 的格式存储，存储的内容是文档型的。这样也就有有机会对某些字段建立索引，实现关系数据库的某些功能。       |
| 列存储（Wide Column Store/Column-Family） | Cassandra  | 顾名思义，是按列存储数据的。最大的特点是方便存储结构化和半结构化数据，方便做数据压缩，对针对某一列或者某几列的查询有非常大的 IO 优势。 |
| 图（Graph-Oriented）                    | Neo4J      | 图形关系的最佳存储。使用传统关系数据库来解决的话性能低下，而且设计使用不方便。                               |

MongoDB 是一个基于分布式文件存储的数据库

MongoDB 和 Node.js 特别配，因为 MongoDB 是基于文档的，文档是按 BSON（JSON 的轻量化二进制格式）存储的，增删改查等管理数据库的命令和 JavaScript 语法很像，这里我们选择 mongoose 来进行增删改查，mongoose 构建在 MongoDB 之上，提供了 Schema、Model 和 Document 对象，用起来很方便

| **SQL 术语/概念** | **MongoDB 术语/概念** | **解释/说明**                  |
| ------------- | ----------------- | -------------------------- |
| database      | database          | 数据库                        |
| table         | collection        | 数据库表/集合                    |
| row           | document          | 数据记录行/文档                   |
| column        | field             | 数据字段/域                     |
| index         | index             | 索引                         |
| table joins   |                   | 表连接,MongoDB 不支持            |
| primary key   | primary key       | 主键,MongoDB 自动将\_id 字段设置为主键 |

Schema ： 一种以文件形式存储的数据库模型骨架，不具备数据库的操作能力

Model ： 由 Schema 发布生成的模型，具有抽象属性和行为的数据库操作对

Entity ： 由 Model 创建的实体，他的操作也会影响数据库

Schema 生成 Model，Model 创造 Entity，Model 和 Entity 都可对数据库操作造成影响，但 Model 比 Entity 更具操作性

### **Schema**

定义：Schema 是用来定义 MongoDB 集合中文档结构的对象。它描述了每个字段的数据类型、验证规则、默认值等属性。然而，Schema 本身并不直接与数据库交互；它只是一个蓝图或模板，用于创建模型。

```
const kittySchema = mongoose.Schema({
    name: String
});
```

**Schema.Type**

Schema.Type 是由 Mongoose 内定的一些数据类型，基本数据类型都在其中，他也内置了一些 Mongoose 特有的 Schema.Type。当然，你也可以自定义 Schema.Type，只有满足 Schema.Type 的类型才能定义在 Schema 内

* String
* Number
* Date
* Buffer
* Boolean
* Mixed
* Objectid
* Array

### **Model**

定义好了 Schema，接下就是生成 Model。 model 是由 schema 生成的模型，可以对数据库的操作

```javascript
var Kitten = mongoose.model('Kitten', kittySchema)
```

**Entity**

用 Model 创建 Entity，Entity 可以对数据库操作

```javascript
var silence = new Kitten({ name: 'Silence' });
console.log(silence.name); // 'Silence'
```

### **查询**

model.find({},field,callback) // 参数 1 忽略,或为空对象则返回所有集合文档

model.find({},{'name':1, 'age':0},callback) //过滤查询,参数 2: {'name':1, 'age':0} 查询文档的返回结果包含 name , 不包含 age.(\_id 默认是 1)

model.find({},null,{limit:20}) // 游标操作 limit 限制返回结果数量为 20 个,如不足 20 个则返回所有

model.findOne({}, callback) // 查询找到的第一个文档

model.findById('obj.\_id', callback) // 查询找到的第一个文档, 只接受 \_id 的值查询

### **创建**

// 在集合中创建一个文档 Model.create(doc(s), \[callback])

Entity.save(callback)

### **删除**

Model.remove(\[criteria], \[callback]) // 根据条件查找到并删除

Model.findByIdAndRemove(id, \[options], \[callback]) // 根据 id 查找到并删除

### **修改**

Model.update(conditions, update, \[options], \[callback]) // 根据参数找到并更新

Model.findByIdAndUpdate(id, \[update], \[options], \[callback]) // 根据 id 查找到并更新



### **如何在 MongoDB 中使用索引优化查询？**

MongoDB 通过索引（Indexes）来加速查询，避免全表扫描。常见索引优化技巧如下：

***

#### **1. 创建索引**

默认情况下，MongoDB 仅在 `_id` 字段上有索引。如果查询涉及其他字段，应手动创建索引：

```javascript
db.users.createIndex({ age: 1 }) // 对 age 字段创建升序索引
db.users.createIndex({ name: 1, age: -1 }) // 复合索引（name 升序，age 降序）
```

***

#### **2. 使用 `explain()` 分析查询**

使用 `explain("executionStats")` 观察查询是否使用了索引：

```javascript
db.users.find({ age: 30 }).explain("executionStats")
```

**关键字段分析：**

* `IXSCAN`（索引扫描）：查询使用了索引，性能较高。
* `COLLSCAN`（全表扫描）：查询未命中索引，需优化。

***

#### **3. 复合索引（Compound Index）**

如果查询同时涉及多个字段，创建**复合索引**比多个单列索引更高效：

```javascript
db.users.createIndex({ name: 1, age: -1 })
```

**优化查询**

```javascript
db.users.find({ name: "Alice", age: { $gt: 20 } }) // 直接利用索引
db.users.find({ age: { $gt: 20 } }) // 可能无法完全利用索引
```

**索引匹配规则**

* 只有**查询条件的最左前缀**才能使用索引。例如：
  * `find({ name: "Alice", age: 30 })` ✅
  * `find({ age: 30 })` ❌（未匹配最左前缀 `name`）

***

#### **4. 唯一索引（Unique Index）**

防止重复插入：

```javascript
db.users.createIndex({ email: 1 }, { unique: true })
```

***

#### **5. TTL 索引（Time-To-Live Index）**

自动删除过期数据（适用于日志、缓存）：

```javascript
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 }) // 1 小时后删除
```

***

#### **6. 局部索引（Partial Index）**

只索引符合特定条件的文档，减少索引大小：

```javascript
db.users.createIndex({ status: 1 }, { partialFilterExpression: { status: { $eq: "active" } } })
```

***

#### **7. 哈希索引**

适用于**等值查询**（但不支持范围查询）：

```javascript
db.users.createIndex({ email: "hashed" })
```

***

### **在高并发场景下，如何提高数据库的读写性能？**

高并发下，MongoDB 可能会面临**读写瓶颈**，以下优化策略有助于提高性能：

#### **1. 读优化**

**（1）主从复制（Replication）**

* **作用**：MongoDB **默认主从复制**，可以通过**从节点分担读压力**。
* **实现**：

```javascript
rs.initiate() // 初始化复制集
rs.add("mongodb-node2:27017") // 添加从节点
rs.status() // 查看状态
```

* **读写分离**：

```javascript
db.getMongo().setReadPref("secondary") // 从节点读
```

***

**（2）使用缓存（Redis + MongoDB）**

在高频查询场景，使用 **Redis 缓存**热点数据：

```javascript
const redis = require("redis");
const client = redis.createClient();

async function getUser(userId) {
  const cacheKey = `user:${userId}`;
  
  // 先查 Redis 缓存
  const cachedData = await client.get(cacheKey);
  if (cachedData) return JSON.parse(cachedData);

  // 缓存未命中，查询 MongoDB
  const user = await db.users.findOne({ _id: userId });

  // 写入 Redis 缓存
  if (user) await client.set(cacheKey, JSON.stringify(user), "EX", 3600); // 1 小时过期

  return user;
}
```

***

**（3）分片（Sharding）**

适用于**海量数据场景**，将数据分布到多个 MongoDB 服务器上：

```javascript
sh.enableSharding("myDatabase") // 启用分片
sh.shardCollection("myDatabase.users", { _id: "hashed" }) // 基于 `_id` 哈希分片
```

***

#### **2. 写优化**

**（1）批量插入（Bulk Insert）**

避免循环单条插入：

```javascript
db.users.insertMany([{ name: "Alice" }, { name: "Bob" }])
```

***

**（2）写操作优化**

* **禁用 `fsync` 以提高写入性能**（适用于临时数据）：

```javascript
db.getSiblingDB("admin").runCommand({ setParameter: 1, journalCommitInterval: 5000 })
```

* **使用 `WriteConcern` 控制写入确认级别**：

```javascript
db.users.insertOne({ name: "Alice" }, { writeConcern: { w: 1, j: false } })
```

* `w: 1`：写入到**主节点**即返回
* `j: false`：不写入日志，提高吞吐量

***

**（3）异步写入（如队列 + MongoDB）**

* 生产环境可使用 **RabbitMQ / Kafka** 缓冲写入，避免高并发直接写入 MongoDB：

```javascript
const amqp = require("amqplib");
async function publishMessage(queue, msg) {
  const conn = await amqp.connect("amqp://localhost");
  const channel = await conn.createChannel();
  await channel.assertQueue(queue);
  channel.sendToQueue(queue, Buffer.from(msg));
}
```

* 消费队列：

```javascript
const { MongoClient } = require("mongodb");
async function consumeMessages(queue) {
  const client = new MongoClient("mongodb://localhost:27017");
  await client.connect();
  const db = client.db("myDatabase");

  // 监听队列，批量写入 MongoDB
  channel.consume(queue, async (msg) => {
    const data = JSON.parse(msg.content.toString());
    await db.collection("users").insertOne(data);
    channel.ack(msg);
  });
}
```

***

#### **总结**

| **优化方向** | **优化策略**                              |
| -------- | ------------------------------------- |
| **读优化**  | 索引优化、主从复制、读写分离、Redis 缓存、分片            |
| **写优化**  | 批量插入、异步写入、降低写入一致性（WriteConcern）、队列写入  |
| **通用优化** | 限制查询返回字段（`projection`）、使用 TTL 索引、适当分片 |

**适用场景**

* **高并发读**：缓存 + 读写分离
* **高并发写**：异步写入（队列）+ 分片
* **实时数据**：索引 + 批量处理

如果你的 MongoDB 处理**上千万级数据量**，可以考虑进一步优化分片策略，或者考虑 **MongoDB Atlas + Auto-scaling**。你是想优化某个具体场景吗？
