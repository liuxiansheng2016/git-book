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
