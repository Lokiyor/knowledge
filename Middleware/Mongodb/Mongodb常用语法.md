# Mongodb常用语法
## 数据库操作
####  查看所有数据库
```
> show dbs
```
#### 查看当前数据库名
```
> db
```
#### 创建数据
```
> use DATABASE_NAME
```
如果数据库不存在，则创建数据库，否则切换到指定数据库
#### 删除数据库
```
> db.dropDatabase()
```
删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。


## 集合操作
#### 查看已有集合
```
> show collections
```
#### 创建集合
```
> db.createCollection(name, options)
```
参数说明：
- name: 要创建的集合名称
- options: 可选参数, 指定有关内存大小及索引的选项

options 可以是如下参数：
字段|类型|描述
---|:--:|---:
capped|布尔|（可选）如果为 true，则创建固定集合。固定集合是指有着固定大小的集合，当达到最大值时，它会自动覆盖最早的文档。**当该值为 true 时，必须指定 size 参数。**
autoIndexId|布尔|（可选）如为 true，自动在 _id 字段创建索引。默认为 false。
size|数值|（可选）为固定集合指定一个最大值，以千字节计（KB）。**如果 capped 为 true，也需要指定该字段。**
max|数值|（可选）指定固定集合中包含文档的最大数量。

在插入文档时，MongoDB 首先检查固定集合的 size 字段，然后检查 max 字段。
例：
```
> db.createCollection("runoob")

> db.createCollection("mycol", {capped:true,autoIndexId:true,size:6142800,max:10000} )
```
#### 删除集合
```
> db.COLLECTION_NAME.drop()
```
## 文档操作
### 插入操作
#### 插入文档
```
> db.COLLECTION_NAME.insert(document)
```
例：
```
> db.col.insert({title: 'MongoDB 教程', 
    description: 'MongoDB 是一个 Nosql 数据库',
    by: '菜鸟教程',
    url: 'http://www.runoob.com',
    tags: ['mongodb', 'database', 'NoSQL'],
    likes: 100
})
```
#### 插入单条数据
```
> db.collection.insertOne({"a": 3})
```
#### 插入多条数据
```
> db.collection.insertMany([{"b": 3}, {'c': 4}])
```

### 更新操作
#### 更新文档 - update() 方法
```
> db.collection.update(
    <query>,
    <update>,
    {
        upsert: <boolean>,
        multi: <boolean>,
        writeConcern: <document>
    }
)
```
参数说明：
- query : update的查询条件，类似sql update查询内where后面的。
- update : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
- upsert : 可选，这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
- multi : 可选，mongodb 默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。
- writeConcern :可选，抛出异常的级别。
  - WriteConcern.NONE:没有异常抛出
  - WriteConcern.NORMAL:仅抛出网络错误异常，没有服务器错误异常
  - WriteConcern.SAFE:抛出网络错误异常、服务器错误异常；并等待服务器完成写操作。
  - WriteConcern.MAJORITY: 抛出网络错误异常、服务器错误异常；并等待一个主服务器完成写操作。
  - WriteConcern.FSYNC_SAFE: 抛出网络错误异常、服务器错误异常；写操作等待服务器将数据刷新到磁盘。
  - WriteConcern.JOURNAL_SAFE:抛出网络错误异常、服务器错误异常；写操作等待服务器提交到磁盘的日志文件。
  - WriteConcern.REPLICAS_SAFE:抛出网络错误异常、服务器错误异常；等待至少2台服务器完成写操作。


#### 更新文档 - save() 方法
```
> db.collection.save(
    <document>,
    {
        writeConcern: <document>
    }
)
```
参数说明：
- document : 文档数据。
- writeConcern :可选，抛出异常的级别。

#### 更新单条和多条记录
```
> db.collection.updateOne()
> db.collection.updateMany()
```

##### 更新实例
```
# 只更新第一条记录：
> db.col.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } );

#全部更新：
> db.col.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true );

# 只添加第一条：
> db.col.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false );

# 全部添加进去:
> db.col.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true );

# 全部更新：
> db.col.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );

# 只更新第一条记录：
> db.col.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );
```

### 删除操作
#### 删除文档
```
> db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
参数说明:
- query :（可选）删除的文档的条件。
- justOne : （可选）如果设为 true 或 1，则只删除一个文档，如果不设置该参数，或使用默认值 false，则删除所有匹配条件的文档。
- writeConcern :（可选）抛出异常的级别。

> remove() 方法 并不会真正释放空间。需要继续执行 db.repairDatabase() 来回收磁盘空间
```
> db.repairDatabase()
# 或者
> db.runCommand({ repairDatabase: 1 })
```

#### 删除单条或多条
```
> db.collection.deleteOne()
> db.collection.deleteMany()
```

### 查询操作
#### 查询文档
```
> db.collection.find(query, projection)
# 易读的方式来读取数据
> db.collection.find().pretty() 
```
参数说明:
- query ：可选，使用查询操作符指定查询条件
- projection ：可选，使用投影操作符指定返回的键。查询时返回文档中所有键值， 只需省略该参数即可（默认省略）。
##### where条件
操作|格式|
---|:---:
等于|{key:value}
小于|{key:{$lt:value}}
小于等于|{key:{$lte:value}}
大于|{key:{$gt:value}}
大于等于|{key:{$gte:value}}
不等于|{key:{$ne:value}}

##### and条件
```
> db.col.find({key1:value1, key2:value2}).pretty()
```
##### or条件
```
> db.col.find({$or:[{key1: value1}, {key2:value2}]}
).pretty()
```
##### 模糊查询
```
# 查询 title 包含"教"字的文档：
> db.col.find({title:/教/})

#查询 title 字段以"教"字开头的文档：
> db.col.find({title:/^教/})

# 查询 titl e字段以"教"字结尾的文档：
> db.col.find({title:/教$/})
```
##### limit条件
参数指定从MongoDB中读取的记录条数。
```
> db.COLLECTION_NAME.find().limit(NUMBER)
```
##### skip条件
跳过指定数量的数据
```
> db.COLLECTION_NAME.find().limit(NUMBER).skip(NUMBER)
```
- 分页查询

想要读取从 10 条记录后 100 条记录，相当于 sql 中limit (10,100)。
```
> db.COLLECTION_NAME.find().skip(10).limit(100)
```
> 补充说明：skip和limit方法只适合小数据量分页，如果是百万级效率就会非常低，因为skip方法是一条条数据数过去的，建议使用where_limit

记录上次查询的最后一个_id,下次查询{_id:{$gt:last_id}},作为前置条件进行分页查询

##### sort条件
```
> db.COLLECTION_NAME.find().sort({KEY:1})
```
-  1: 升序
-  2: 降序

#### 其他操作
##### 索引
创建索引
```
> db.collection.createIndex(keys, options)
```
- keys: 索引字段
- options: 可选参数
 
参数|类型|描述
---|:--:|---:
background|Boolean|建索引过程会阻塞其它数据库操作，background可指定以后台方式创建索引，即增加 "background" 可选参数。 "background" 默认值为**false**。
unique|Boolean|建立的索引是否唯一。指定为true创建唯一索引。默认值为**false**。
name|string|索引的名称。如果未指定，MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。
dropDups|Boolean|~~3.0+版本已废弃~~。在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为**false**。
sparse|Boolean|对文档中不存在的字段数据不启用索引；这个参数需要特别注意，如果设置为true的话，在索引字段中不会查询出不包含对应字段的文档。默认值为**false**。
expireAfterSeconds|integer|	指定一个以秒为单位的数值，完成 TTL设定，设定集合的生存时间。
v|index version|索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。
weights|document|索引权重值，数值在 1 到 99,999 之间，表示该索引相对于其他索引字段的得分权重。
default_language|string|对于文本索引，该参数决定了停用词及词干和词器的规则的列表。 默认为英语
language_override|string|对于文本索引，该参数指定了包含在文档中的字段名，语言覆盖默认的language，默认值为 language。

- 其他索引操作
```
# 查看集合索引
> db.col.getIndexes()

# 查看集合索引大小
> db.col.totalIndexSize()

# 删除集合所有索引
> db.col.dropIndexes()

# 删除集合指定索引
> db.col.dropIndex("索引名称")
```

##### 聚合操作
处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的 count(*)。
```
> db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)
```
- 例：
```
> db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : 1}}}])
# 等于sql中以下语法
> select by_user, count(*) from mycol group by by_user
```
- 聚合的表达式:

表达式|描述|实例
---|:--:|---:
$sum|计算总和|db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$sum : "\$likes"}}}])
$avg|计算平均值|db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$avg : "\$likes"}}}])
$min|获取集合中所有文档对应值得最小值|db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$min : "$likes"}}}])
$max|获取集合中所有文档对应值得最大值|db.mycol.aggregate([{\$group : {_id : "\$by_user", num_tutorial : {\$max : "$likes"}}}])
$push|在结果文档中插入值到一个数组中|db.mycol.aggregate([{\$group : {_id : "\$by_user", url : {\$push: "\$url"}}}])
$addToSet|在结果文档中插入值到一个数组中，但不创建副本|db.mycol.aggregate([{\$group : {_id : "\$by_user", url : {\$addToSet : "\$url"}}}])
$first|根据资源文档的排序获取第一个文档数据|db.mycol.aggregate([{\$group : {_id : "\$by_user", first_url : {\$first : "\$url"}}}])
$last|根据资源文档的排序获取最后一个文档数据|db.mycol.aggregate([{\$group : {_id : "\$by_user", last_url : {\$last : "\$url"}}}])


###### 管道的概念


> 当 match 条件和 group 同时存在时，顺序会影响检索结果，必须先写 match 在前面。