# ES相关问题解决办法
#### Cluster health status changed from [RED] to [YELLOW] (reason: [shards started [[.kibana_task_manager_1][0]]]).
由于我们是单节点部署elasticsearch，而默认的分片副本数目配置为1，而相同的分片不能在一个节点上，所以就存在副本分片指定不明确的问题，所以显示为yellow，
我们可以通过在elasticsearch集群上添加一个节点来解决问题，
如果你不想这么做，你可以删除那些指定不明确的副本分片（当然这不是一个好办法）但是作为测试和解决办法还是可以尝试的

解决方法 :以下两种
1. curl -XPUT “http://localhost:9200/_settings” -d’ { “number_of_replicas” : 0 } ’ #删除那些指定不明确的副本分片
2. 将E做成集群，不做单机，完成后，健康状态将变成绿色
