# MongoDB复杂操作
文章来源：[https://www.runoob.com/mongodb/mongodb-tutorial.html](https://www.runoob.com/mongodb/mongodb-tutorial.html)
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

## MongoDB 原子操作
mongodb不支持事务，所以，在你的项目中应用时，要注意这点。无论什么设计，都不要要求mongodb保证数据的完整性。  
但是mongodb提供了许多原子操作，比如文档的保存，修改，删除等，都是原子操作。  
所谓原子操作就是要么这个文档保存到Mongodb，要么没有保存到Mongodb，不会出现查询到的文档没有保存完整的情况。
### 原子操作常用命令
#### $set
用来指定一个键并更新键值，若键不存在并创建。
```
{ $set : { field : value } }
```
#### $unset
用来删除一个键。
```
{ $unset : { field : 1} }
```
#### $inc
$inc可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作。
```
{ $inc : { field : value } }
```
#### $push
用法：
```
{ $push : { field : value } }
```
把value追加到field里面去，field一定要是数组类型才行，如果field不存在，会新增一个数组类型加进去。
#### $pushAll
同$push,只是一次可以追加多个值到一个数组字段内。
```
{ $pushAll : { field : value_array } }
```
#### $pull
从数组field内删除一个等于value值。
```
{ $pull : { field : _value } }
```
#### $addToSet
增加一个值到数组内，而且只有当这个值不在数组内才增加。

#### $pop
删除数组的第一个或最后一个元素
```
{ $pop : { field : 1 } }
```
#### $rename
修改字段名称
```
{ $rename : { old_field_name : new_field_name } }
```
#### $bit
位操作，integer类型
```
{$bit : { field : {and : 5}}}
```

## MongoDB 高级索引
例文档集合:
```
{
   "address": {
      "city": "Los Angeles",
      "state": "California",
      "pincode": "123"
   },
   "tags": [
      "music",
      "cricket",
      "blogs"
   ],
   "name": "Tom Benzamin"
}
```
以上文档包含了 address 子文档和 tags 数组。

### 索引数组字段
- 在数组中创建索引，需要对数组中的每个字段依次建立索引。所以在我们为数组 tags 创建索引时，会为 music、cricket、blogs三个值建立单独的索引。  
  
使用以下命令创建数组索引：
```
> db.users.ensureIndex({"tags":1})
```
创建索引后，我们可以这样检索集合的 tags 字段：
```
> db.users.find({tags:"cricket"})
```
为了验证我们使用使用了索引，可以使用 explain 命令：
```
> db.users.find({tags:"cricket"}).explain()
```
以上命令执行结果中会显示 "cursor" : "BtreeCursor tags_1" ，则表示已经使用了索引。

### 索引子文档字段
假设我们需要通过city、state、pincode字段来检索文档，由于这些字段是子文档的字段，所以我们需要对子文档建立索引。  
为子文档的三个字段创建索引，命令如下：
```
> db.users.ensureIndex({"address.city":1,"address.state":1,"address.pincode":1})
```
一旦创建索引，我们可以使用子文档的字段来检索数据：
```
> db.users.find({"address.city":"Los Angeles"})   
```
查询表达不一定遵循指定的索引的顺序，mongodb 会自动优化。所以上面创建的索引将支持以下查询：
```
> db.users.find({"address.state":"California","address.city":"Los Angeles"}) 
```
同样支持以下查询：
```
> db.users.find({"address.city":"Los Angeles","address.state":"California","address.pincode":"123"})
```

## MongoDB 索引限制
### 额外开销
每个索引占据一定的存储空间，在进行插入，更新和删除操作时也需要对索引进行操作。所以，如果很少对集合进行读取操作，建议不使用索引。
### 内存(RAM)使用
由于索引是存储在内存(RAM)中,你应该确保该索引的大小不超过内存的限制。  
如果索引的大小大于内存的限制，MongoDB会删除一些索引，这将导致性能下降。
### 查询限制
索引不能被以下的查询使用：
- 正则表达式及非操作符，如 $nin, $not, 等。
- 算术运算符，如 $mod, 等。
- $where 子句  

所以，检测你的语句是否使用索引是一个好的习惯，可以用explain来查看。
### 索引键限制
从2.6版本开始，如果现有的索引字段的值超过索引键的限制，MongoDB中不会创建索引。
### 插入文档超过索引键限制
如果文档的索引字段值超过了索引键的限制，MongoDB不会将任何文档转换成索引的集合。  
与mongorestore和mongoimport工具类似。
### 最大范围
- 集合中索引不能超过64个
- 索引名的长度不能超过128个字符
- 一个复合索引最多可以有31个字段

## MongoDB ObjectId
### 基本盖概念
ObjectId 是一个12字节 BSON 类型数据，有以下格式：
- 前4个字节表示时间戳
- 接下来的3个字节是机器标识码
- 紧接的两个字节由进程id组成（PID）
- 最后三个字节是随机数。

MongoDB中存储的文档必须有一个"_id"键。这个键的值可以是任何类型的，默认是个ObjectId对象。  
在一个集合里面，每个文档都有唯一的"_id"值，来确保集合里面每个文档都能被唯一标识。  
MongoDB采用ObjectId，而不是其他比较常规的做法（比如自动增加的主键）的主要原因，因为在多个 服务器上同步自动增加主键值既费力还费时。

### 创建新的ObjectId
使用以下代码生成新的ObjectId：
```
> newObjectId = ObjectId()
```
上面的语句返回以下唯一生成的id：
```
ObjectId("5349b4ddd2781d08c09890f3")
```
### 创建文档的时间戳
由于 ObjectId 中存储了 4 个字节的时间戳，所以你不需要为你的文档保存时间戳字段，你可以通过 getTimestamp 函数来获取文档的创建时间:
```
> ObjectId("5349b4ddd2781d08c09890f4").getTimestamp()
```
以上代码将返回 ISO 格式的文档创建时间：
```
ISODate("2014-04-12T21:49:17Z")
```
### ObjectId 转换为字符串
在某些情况下，您可能需要将ObjectId转换为字符串格式。你可以使用下面的代码：
```
> new ObjectId().str
```
以上代码将返回Guid格式的字符串：：
```
5349b4ddd2781d08c09890f3
```

## MongoDB Map Reduce
Map-Reduce是一种计算模型，简单的说就是将大批量的工作（数据）分解（MAP）执行，然后再将结果合并成最终结果（REDUCE）。  
MongoDB提供的Map-Reduce非常灵活，对于大规模数据分析也相当实用。
### MapReduce 命令
```
> db.collection.mapReduce(
   function() {emit(key,value);},  //map 函数
   function(key,values) {return reduceFunction},   //reduce 函数
   {
      out: collection,
      query: document,
      sort: document,
      limit: number
   }
)
```
使用 MapReduce 要实现两个函数 Map 函数和 Reduce 函数,Map 函数调用 emit(key, value), 遍历 collection 中所有的记录, 将 key 与 value 传递给 Reduce 函数进行处理。  
Map 函数必须调用 emit(key, value) 返回键值对。  
参数说明:
- map 映射函数 (生成键值对序列,作为 reduce 函数参数)。
- reduce 统计函数，reduce函数的任务就是将key-values变成key-value，也就是把values数组变成一个单一的值value。。
- out 统计结果存放集合 (不指定则使用临时集合,在客户端断开后自动删除)。
- query 一个筛选条件，只有满足条件的文档才会调用map函数。（query。limit，sort可以随意组合）
- sort 和limit结合的sort排序参数（也是在发往map函数前给文档排序），可以优化分组机制
- limit 发往map函数的文档数量的上限（要是没有limit，单独使用sort的用处不大）

以下实例在集合 orders 中查找 status:"A" 的数据，并根据 cust_id 来分组，并计算 amount 的总和。  
输出结果：
```
{
        "result" : "post_total",
        "timeMillis" : 23,
        "counts" : {
                "input" : 5,
                "emit" : 5,
                "reduce" : 1,
                "output" : 2
        },
        "ok" : 1
}
```
结果表明，共有 5 个符合查询条件（status:"active"）的文档， 在map函数中生成了 5 个键值对文档，最后使用reduce函数将相同的键值分为 2 组。  
具体参数说明：
- result：储存结果的collection的名字,这是个临时集合，MapReduce的连接关闭后自动就被删除了。
- timeMillis：执行花费的时间，毫秒为单位
- input：满足条件被发送到map函数的文档个数
- emit：在map函数中emit被调用的次数，也就是所有集合中的数据总量
- ouput：结果集合中的文档个数（count对调试非常有帮助）
- ok：是否成功，成功为1
- err：如果失败，这里可以有失败原因，不过从经验上来看，原因比较模糊，作用不大

## MongoDB 全文检索
全文检索对每一个词建立一个索引，指明该词在文章中出现的次数和位置，当用户查询时，检索程序就根据事先建立的索引进行查找，并将查找的结果反馈给用户的检索方式。  
这个过程类似于通过字典中的检索字表查字的过程。
### 启用全文检索
MongoDB 在 2.6 版本以后是默认开启全文检索的，如果你使用之前的版本，你需要使用以下代码来启用全文检索:
```
> db.adminCommand({setParameter:true,textSearchEnabled:true})
```
或者使用命令：
```
$ mongod --setParameter textSearchEnabled=true
```
### 创建全文索引
考虑以下 posts 集合的文档数据，包含了文章内容（post_text）及标签(tags)：
```
{
   "post_text": "enjoy the mongodb articles on Runoob",
   "tags": [
      "mongodb",
      "runoob"
   ]
}
```
我们可以对 post_text 字段建立全文索引，这样我们可以搜索文章内的内容：
```
> db.posts.ensureIndex({post_text:"text"})
```
### 使用全文索引
现在我们已经对 post_text 建立了全文索引，我们可以搜索文章中的关键词 runoob：
```
> db.posts.find({$text:{$search:"runoob"}})
```
以下命令返回了如下包含 runoob 关键词的文档数据：
```
{ 
   "_id" : ObjectId("53493d14d852429c10000002"), 
   "post_text" : "enjoy the mongodb articles on Runoob", 
   "tags" : [ "mongodb", "runoob" ]
}
```
如果你使用的是旧版本的 MongoDB，你可以使用以下命令：
```
> db.posts.runCommand("text",{search:"runoob"})
```
使用全文索引可以提高搜索效率。

### 删除全文索引
删除已存在的全文索引，可以使用 find 命令查找索引名：
```
> db.posts.getIndexes()
```
通过以上命令获取索引名，本例的索引名为post_text_text，执行以下命令来删除索引：
```
> db.posts.dropIndex("post_text_text")
```

## MongoDB 正则表达式
MongoDB 使用 $regex 操作符来设置匹配字符串的正则表达式。  
MongoDB使用PCRE (Perl Compatible Regular Expression) 作为正则表达式语言。  
不同于全文检索，我们使用正则表达式不需要做任何配置。
### 使用正则表达式
以下命令使用正则表达式查找posts集合中包含 runoob 字符串的文章：
```
> db.posts.find({post_text:{$regex:"runoob"}})
```
以上查询也可以写为：
```
> db.posts.find({post_text:/runoob/})
```
### 不区分大小写的正则表达式
如果检索需要不区分大小写，我们可以设置 $options 为 $i。  
以下命令将查找不区分大小写的字符串 runoob：
```
> db.posts.find({post_text:{$regex:"runoob",$options:"$i"}})
```
集合中会返回所有包含字符串 runoob 的数据，且不区分大小写：
```
{
   "_id" : ObjectId("53493d37d852429c10000004"),
   "post_text" : "hey! this is my post on  runoob", 
   "tags" : [ "runoob" ]
} 
```
### 数组元素使用正则表达式
我们还可以在数组字段中使用正则表达式来查找内容。 这在标签的实现上非常有用，如果你需要查找包含以 run 开头的标签数据(ru 或 run 或 runoob)， 你可以使用以下代码：
```
> db.posts.find({tags:{$regex:"run"}})
```
### 优化正则表达式查询
如果你的文档中字段设置了索引，那么使用索引相比于正则表达式匹配查找所有的数据查询速度更快。  
如果正则表达式是前缀表达式，所有匹配的数据将以指定的前缀字符串为开始。例如： 如果正则表达式为 ^tut ，查询语句将查找以 tut 为开头的字符串。   
这里面使用正则表达式有两点需要注意：  
正则表达式中使用变量。一定要使用eval将组合的字符串进行转换，不能直接将字符串拼接后传入给表达式。否则没有报错信息，只是结果为空！实例如下：
```
var name=eval("/" + 变量值key +"/i"); 
```
以下是模糊查询包含title关键词, 且不区分大小写:
```
title:eval("/"+title+"/i")    // 等同于 title:{$regex:title,$Option:"$i"}   
```
##### $regex操作符的使用
$regex操作符中的option选项可以改变正则匹配的默认行为，它包括i, m, x以及S四个选项，其含义如下

- i: 忽略大小写，{\<field\>{\$regex/pattern/i}}，设置i选项后，模式中的字母会进行大小写不敏感匹配。
- m: 多行匹配模式，{\<field\>{\$regex/pattern/,\$options:'m'}，m选项会更改^和$元字符的默认行为，分别使用与行的开头和结尾匹配，而不是与输入字符串的开头和结尾匹配。
- x: 忽略非转义的空白字符，{\<field\>:{\$regex:/pattern/,\$options:'m'}，设置x选项后，正则表达式中的非转义的空白字符将被忽略，同时井号(#)被解释为注释的开头注，只能显式位于option选项中。
- s: 单行匹配模式{\<field\>:{\$regex:/pattern/,\$options:'s'}，设置s选项后，会改变模式中的点号(.)元字符的默认行为，它会匹配所有字符，包括换行符(\n)，只能显式位于option选项中。
使用$regex操作符时，需要注意下面几个问题:

> i，m，x，s可以组合使用，例如:{name:{\$regex:/j*k/,\$options:"si"}}
在设置索弓}的字段上进行正则匹配可以提高查询速度，而且当正则表达式使用的是前缀表达式时，查询速度会进一步提高，例如:{name:{$regex: /^joe/}


## MongoDB 管理工具
- Studio 3T (推荐)
- Robo 3T (简化版)
- Navicat for MongoDB
- Rockmongo

## MongoDB GridFS
GridFS 用于存储和恢复那些超过16M（BSON文件限制）的文件(如：图片、音频、视频等)。  
GridFS 也是文件存储的一种方式，但是它是存储在MonoDB的集合中。  
GridFS 可以更好的存储大于16M的文件。  
GridFS 会将大文件对象分割成多个小的chunk(文件片段),一般为256k/个,每个chunk将作为MongoDB的一个文档(document)被存储在chunks集合中。  
GridFS 用两个集合来存储一个文件：fs.files与fs.chunks。  
每个文件的实际内容被存在chunks(二进制数据)中,和文件有关的meta数据(filename,content_type,还有用户自定义的属性)将会被存在files集合中。  
以下是简单的 fs.files 集合文档：
```
{
   "filename": "test.txt",
   "chunkSize": NumberInt(261120),
   "uploadDate": ISODate("2014-04-13T11:32:33.557Z"),
   "md5": "7b762939321e146569b07f72c62cca4f",
   "length": NumberInt(646)
}
```
以下是简单的 fs.chunks 集合文档：
```
{
   "files_id": ObjectId("534a75d19f54bfec8a2fe44b"),
   "n": NumberInt(0),
   "data": "Mongo Binary Data"
}
```

## MongoDB 固定集合（Capped Collections）
MongoDB 固定集合（Capped Collections）是性能出色且有着固定大小的集合，对于大小固定，我们可以想象其就像一个环形队列，当集合空间用完后，再插入的元素就会覆盖最初始的头部的元素！
### 创建固定集合
我们通过createCollection来创建一个固定集合，且capped选项设置为true：
```
> db.createCollection("cappedLogCollection",{capped:true,size:10000})
```
还可以指定文档个数,加上max:1000属性：
```
> db.createCollection("cappedLogCollection",{capped:true,size:10000,max:1000})
```
判断集合是否为固定集合:
```
> db.cappedLogCollection.isCapped()
```
如果需要将已存在的集合转换为固定集合可以使用以下命令：
```
> db.runCommand({"convertToCapped":"posts",size:10000})
```
以上代码将我们已存在的 posts 集合转换为固定集合。

- size 是整个集合空间大小，单位为【KB】
- max 是集合文档个数上线，单位是【个】
### 固定集合查询
固定集合文档按照插入顺序储存的,默认情况下查询就是按照插入顺序返回的,也可以使用$natural调整返回顺序。
```
> db.cappedLogCollection.find().sort({$natural:-1})
```
### 固定集合的功能特点
可以插入及更新,但更新不能超出collection的大小,否则更新失败,不允许删除,但是可以调用drop()删除集合中的所有行,但是drop后需要显式地重建集合。   
在32位机子上一个cappped collection的最大值约为482.5M,64位上只受系统文件大小的限制。
### 固定集合属性及用法
#### 属性
属性1:对固定集合进行插入速度极快
属性2:按照插入顺序的查询输出速度极快
属性3:能够在插入最新数据时,淘汰最早的数据
#### 用法
用法1:储存日志信息
用法2:缓存一些少量的文档

## MongoDB 自动增长
MongoDB 没有像 SQL 一样有自动增长的功能， MongoDB 的 _id 是系统自动生成的12字节唯一标识。  
但在某些情况下，我们可能需要实现 ObjectId 自动增长功能。  
由于 MongoDB 没有实现这个功能，我们可以通过编程的方式来实现，以下我们将在 counters 集合中实现_id字段自动增长。
### 使用 counters 集合
考虑以下 products 文档。我们希望 _id 字段实现 从 1,2,3,4 到 n 的自动增长功能。
```
{
  "_id":1,
  "product_name": "Apple iPhone",
  "category": "mobiles"
}
```
为此，创建 counters 集合，序列字段值可以实现自动长：
```
> db.createCollection("counters")
```
现在我们向 counters 集合中插入以下文档，使用 productid 作为 key:
```
{
  "_id":"productid",
  "sequence_value": 0
}
```
sequence_value 字段是序列通过自动增长后的一个值。  
使用以下命令插入 counters 集合的序列文档中：
```
> db.counters.insert({_id:"productid",sequence_value:0})
```
### 创建 Javascript 函数
现在，我们创建函数 getNextSequenceValue 来作为序列名的输入， 指定的序列会自动增长 1 并返回最新序列值。在本文的实例中序列名为 productid 。
```
> function getNextSequenceValue(sequenceName){
   var sequenceDocument = db.counters.findAndModify(
      {
         query:{_id: sequenceName },
         update: {$inc:{sequence_value:1}},
         "new":true
      });
   return sequenceDocument.sequence_value;
}
```
### 使用 Javascript 函数
接下来我们将使用 getNextSequenceValue 函数创建一个新的文档， 并设置文档 _id 自动为返回的序列值：
```
> db.products.insert({
   "_id":getNextSequenceValue("productid"),
   "product_name":"Apple iPhone",
   "category":"mobiles"})

> db.products.insert({
   "_id":getNextSequenceValue("productid"),
   "product_name":"Samsung S3",
   "category":"mobiles"})
```
就如你所看到的，我们使用 getNextSequenceValue 函数来设置 _id 字段。  
为了验证函数是否有效，我们可以使用以下命令读取文档：
```
> db.products.find()
```
以上命令将返回以下结果，我们发现 _id 字段是自增长的：
```
{ "_id" : 1, "product_name" : "Apple iPhone", "category" : "mobiles"}
{ "_id" : 2, "product_name" : "Samsung S3", "category" : "mobiles" }
```