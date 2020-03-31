# ES的相关操作
#### 查看索引信息
- GET 'http://localhost:9200/_cluster/health?pretty=true'
- 信息:
```
{
  "cluster_name" : "test",
  "status" : "yellow",      #红黄蓝状态
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 134,
  "active_shards" : 134,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 30,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 98.19711538461539  #数据的正常率，100表示一切ok
}
```

#### 查看所有所有的状态
- 'localhost:9200/_cat/indices?v'

#### 删除那些指定不明确的副本分片
-  PUT “http://localhost:9200/_settings” -d’ { “number_of_replicas” : 0 } 
- 返回:
```
{
    "acknowledged": true
}
```