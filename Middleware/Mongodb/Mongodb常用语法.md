# Mongodb常用语法
## 数据库操作
-  查看所有数据库
```
> show dbs
```
- 查看当前数据库名
```
> db
```
- 创建数据
```
> use DATABASE_NAME
```
如果数据库不存在，则创建数据库，否则切换到指定数据库
- 删除数据库
```
> db.dropDatabase()
```
删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。


## 集合操作
- 查看已有集合
```
> show collections
```
- 创建集合
```
> db.createCollection(name, options)
```
参数说明：
> name: 要创建的集合名称

> options: 可选参数, 指定有关内存大小及索引的选项

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
- 删除集合
```
> db.COLLECTION_NAME.drop()
```
## 文档操作
- 插入文档
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
- 插入单条数据
```
> db.collection.insertOne({"a": 3})
```
- 插入多条数据
```
> db.collection.insertMany([{"b": 3}, {'c': 4}])
```

- 更新文档
```