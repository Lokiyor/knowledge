# Mongodb的其他操作
## MongoDB 复制（副本集）
MongoDB复制是将数据同步在多个服务器的过程。
### 副本集设置
#### 启动mongodb
```
$ mongod --port "PORT" --dbpath "YOUR_DB_DATA_PATH" --replSet "REPLICA_SET_INSTANCE_NAME"
```
以上实例会启动一个名为(REPLICA_SET_INSTANCE_NAME)的MongoDB实例，其端口号为(PORT)。

##### 在Mongo客户端使用以下命令
- 启动一个新的副本集
```
> rs.initiate()
```  
- 查看副本集的配置
```
> rs.conf()
```
- 查看副本集状态
```
> rs.status()
```
- 添加副本集成员
```
> rs.add(HOST_NAME:PORT)
```




## Mongodb 分片
在Mongodb里面存在另一种集群，就是分片技术,可以满足MongoDB数据量大量增长的需求。

### 分片的集群结构
主要有三个组件：
- Shard：用于存储实际的数据块，实际生产环境中一个shard server角色可由几台机器组个一个replica set承担，防止主机单点故障
- Config Server：mongod实例，存储了整个 ClusterMetadata，其中包括 chunk信息
- Query Routers：前端路由，客户端由此接入，且让整个集群看上去像单一数据库，前端应用可以透明使用


## Mongodb 备份与恢复
### 数据备份命令
```
> mongodump -h dbhost -d dbname -o dbdirectory
```
- \-h：MongDB所在服务器地址，例如：127.0.0.1，当然也可以指定端口号：127.0.0.1:27017
- \-d：需要备份的数据库实例，例如：test
- \-o：备份的数据存放位置，例如：c:\data\dump，当然该目录需要提前建立，在备份完成后，系统自动在dump目录下建立一个test目录，这个目录里面存放该数据库实例的备份数据。
#### 其他可选参数
- --host：服务器地址
- --post：端口
- --dbpath：数据库路径
- --out：备份路径
- --collection：指定集合
- --db：数据库

### 数据恢复命令
```
> mongorestore -h <hostname><:port> -d dbname <path>
```
- --host <:port>, -h <:port>：MongoDB所在服务器地址，默认为： localhost:27017
- --db,-d：需要恢复的数据库实例，例如：test，当然这个名称也可以和备份时候的不一样，比如test2
- --drop：恢复的时候，先删除当前数据，然后恢复备份的数据。就是说，恢复后，备份后添加修改的数据都会被删除，慎用哦！
- \<path>：mongorestore 最后的一个参数，设置备份数据所在位置，例如：c:\data\dump\test。你不能同时指定 \<path> 和 --dir 选项，--dir也可以设置备份目录。
- --dir：指定备份的目录。你不能同时指定 \<path> 和 --dir 选项。

## Mongodb 监控
MongoDB中提供了mongostat 和 mongotop 两个命令来监控MongoDB的运行情况。
### mongostat 命令
mongostat是mongodb自带的状态检测工具，在命令行下使用。它会间隔固定时间获取mongodb的当前运行状态，并输出。如果你发现数据库突然变慢或者有其他问题的话，你第一手的操作就考虑采用mongostat来查看mongo的状态。
### mongotop 命令
mongotop也是mongodb下的一个内置工具，mongotop提供了一个方法，用来跟踪一个MongoDB的实例，查看哪些大量的时间花费在读取和写入数据。 mongotop提供每个集合的水平的统计数据。默认情况下，mongotop返回值的每一秒。
#### 参数解释:
- ns：包含数据库命名空间，后者结合了数据库名称和集合。
- db：包含数据库的名称。名为 . 的数据库针对全局锁定，而非特定数据库。
- total：mongod花费的时间工作在这个命名空间提供总额。
- read：提供了大量的时间，这mongod花费在执行读操作，在此命名空间。
- write：提供这个命名空间进行写操作，这mongod花了大量的时间。

