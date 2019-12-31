# MongoDB复杂操作
## MongoDB关系
##### 对应关系
- 1:1 (1对1)
- 1: N (1对多)
- N: 1 (多对1)
- N: N (多对多)

### 嵌入式关系
使用嵌入式方法，我们可以把用户地址嵌入到用户的文档中：
```
{
   "_id":ObjectId("52ffc33cd85242f436000001"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin",
   "address": [
      {
         "building": "22 A, Indiana Apt",
         "pincode": 123456,
         "city": "Los Angeles",
         "state": "California"
      },
      {
         "building": "170 A, Acropolis Apt",
         "pincode": 456789,
         "city": "Chicago",
         "state": "Illinois"
      }]
} 
```
以上数据保存在单一的文档中，可以比较容易的获取和维护数据。 你可以这样查询用户的地址：
```
> db.users.findOne({"name":"Tom Benzamin"},{"address":1})
```
注意：以上查询中 db 和 users 表示数据库和集合。  
这种数据结构的缺点是，如果用户和用户地址在不断增加，数据量不断变大，会影响读写性能。

### 引用式关系
引用式关系是设计数据库时经常用到的方法，这种方法把用户数据文档和用户地址数据文档分开，通过引用文档的 id 字段来建立关系。
```
{
   "_id":ObjectId("52ffc33cd85242f436000001"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin",
   "address_ids": [
      ObjectId("52ffc4a5d85242602e000000"),
      ObjectId("52ffc4a5d85242602e000001")
   ]
}
```
以上实例中，用户文档的 address_ids 字段包含用户地址的对象id（ObjectId）数组。  
我们可以读取这些用户地址的对象id（ObjectId）来获取用户的详细地址信息。  
这种方法需要两次查询，第一次查询用户地址的对象id（ObjectId），第二次通过查询的id获取用户的详细地址信息。
```
> var result = db.users.findOne({"name":"Tom Benzamin"},{"address_ids":1})
> var addresses = db.address.find({"_id":{"$in":result["address_ids"]}})
```

## MongoDB 数据库引用  
MongoDB 引用有两种：
- 手动引用（Manual References）
- DBRefs
> DBRefs vs 手动引用  
考虑这样的一个场景，我们在不同的集合中 (address_home, address_office, address_mailing, 等)存储不同的地址（住址，办公室地址，邮件地址等）。  
这样，我们在调用不同地址时，也需要指定集合，一个文档从多个集合引用文档，我们应该使用 DBRefs。

#### 使用 DBRefs
```
{ $ref : , $id : , $db :  }
```
三个字段表示的意义为：
- $ref：集合名称
- $id：引用的id
- $db：数据库名称，可选参数

实例：
```
{
   "_id":ObjectId("53402597d852426020000002"),
   "address": {
   "$ref": "address_home",
   "$id": ObjectId("534009e4d852427820000002"),
   "$db": "runoob"},
   "contact": "987654321",
   "dob": "01-01-1991",
   "name": "Tom Benzamin"
}
```
address DBRef 字段指定了引用的地址文档是在 runoob 数据库下的 address_home 集合，id 为 534009e4d852427820000002。  
以下代码中，我们通过指定 $ref 参数（address_home 集合）来查找集合中指定id的用户地址信息：
```
>var user = db.users.findOne({"name":"Tom Benzamin"})
>var dbRef = user.address
>db[dbRef.$ref].findOne({"_id":ObjectId(dbRef.$id)})
```


## MongoDB 覆盖索引查询
覆盖查询是以下的查询:
- 所有的查询字段是索引的一部分
- 所有的查询返回字段在同一个索引中  

由于所有出现在查询中的字段是索引的一部分， MongoDB 无需在整个数据文档中检索匹配查询条件和返回使用相同索引的查询结果。  
因为索引存在于RAM中，从索引中获取数据比通过扫描文档读取数据要快得多。

#### 使用覆盖索引查询
users 集合:
```
{
   "_id": ObjectId("53402597d852426020000002"),
   "contact": "987654321",
   "dob": "01-01-1991",
   "gender": "M",
   "name": "Tom Benzamin",
   "user_name": "tombenzamin"
}
```
创建联合索引，字段为 gender 和 user_name :
```
> db.users.ensureIndex({gender:1,user_name:1})
```
现在，该索引会覆盖以下查询：
```
> db.users.find({gender:"M"},{user_name:1,_id:0})
```
也就是说，对于上述查询，MongoDB的不会去数据库文件中查找。相反，它会从索引中提取数据，这是非常快速的数据查询。  
由于我们的索引中不包括 _id 字段，_id在查询中会默认返回，我们可以在MongoDB的查询结果集中排除它。  
下面的实例没有排除_id，查询就不会被覆盖：
```
> db.users.find({gender:"M"},{user_name:1})
```
最后，如果是以下的查询，不能使用覆盖索引查询：
- 所有索引字段是一个数组  

所有索引字段是一个子文档

## MongoDB 查询分析
MongoDB 查询分析可以确保我们所建立的索引是否有效，是查询语句性能分析的重要工具。  
MongoDB 查询分析常用函数有：explain() 和 hint()。
#### 使用 explain()
explain 操作提供了查询信息，使用索引及查询统计等。有利于我们对索引的优化。  
接下来我们在 users 集合中创建 gender 和 user_name 的索引：
```
>db.users.ensureIndex({gender:1,user_name:1})
```
现在在查询语句中使用 explain ：
```
>db.users.find({gender:"M"},{user_name:1,_id:0}).explain()
```
以上的 explain() 查询返回如下结果：
```
{
   "cursor" : "BtreeCursor gender_1_user_name_1",
   "isMultiKey" : false,
   "n" : 1,
   "nscannedObjects" : 0,
   "nscanned" : 1,
   "nscannedObjectsAllPlans" : 0,
   "nscannedAllPlans" : 1,
   "scanAndOrder" : false,
   "indexOnly" : true,
   "nYields" : 0,
   "nChunkSkips" : 0,
   "millis" : 0,
   "indexBounds" : {
      "gender" : [
         [
            "M",
            "M"
         ]
      ],
      "user_name" : [
         [
            {
               "$minElement" : 1
            },
            {
               "$maxElement" : 1
            }
         ]
      ]
   }
}
```
字段描述：
- indexOnly: 字段为 true ，表示我们使用了索引。
- cursor：因为这个查询使用了索引，MongoDB 中索引存储在B树结构中，所以这是也使用了 BtreeCursor 类型的游标。如果没有使用索引，游标的类型是 BasicCursor。这个键还会给出你所使用的索引的名称，你通过这个名称可以查看当前数据库下的system.indexes集合（系统自动创建，由于存储索引信息，这个稍微会提到）来得到索引的详细信息。
- n：当前查询返回的文档数量。
- nscanned/nscannedObjects：表明当前这次查询一共扫描了集合中多少个文档，我们的目的是，让这个数值和返回文档的数量越接近越好。
- millis：当前查询所需时间，毫秒数。
- indexBounds：当前查询具体使用的索引。

#### 使用 hint()
虽然MongoDB查询优化器一般工作的很不错，但是也可以使用 hint 来强制 MongoDB 使用一个指定的索引。  
这种方法某些情形下会提升性能。 一个有索引的 collection 并且执行一个多字段的查询(一些字段已经索引了)。  
如下查询实例指定了使用 gender 和 user_name 索引字段来查询：
```
>db.users.find({gender:"M"},{user_name:1,_id:0}).hint({gender:1,user_name:1})
```
可以使用 explain() 函数来分析以上查询：
```
>db.users.find({gender:"M"},{user_name:1,_id:0}).hint({gender:1,user_name:1}).explain()
```