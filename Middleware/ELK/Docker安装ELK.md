# Docker安装ELK
**ES和kibana的版本尽可能的保证一致**  
**Docker容器下的数据和配置尽量挂在宿主机目录，否则重启容器数据将丢失**
> Docker挂载的目录记得赋予权限(chmod 777 /data/xxx)，否则docker容器用户将无法写入
## 准备镜像
- 获取ES镜像：docker pull elasticsearch:[version]
- 获取Kibana镜像：docker pull kibana:[version]
- 获取Logstash镜像：docker pull logstash:[version]

## 安装 ElasticSearch
#### 启动es容器
通过docker容器启动e，命令如下:
```
> docker run --name es -p 9200:9200 -p 9300:9300 -v /data/docker/es/data:/usr/share/elasticsearch/data -v /data/docker/es/logs:/usr/share/elasticsearch/logs -e "discovery.type=single-node" -d elasticsearch:7.5.1
```
- --name: 启动容器的名称
- -p: 启动的端口 
    - ：前为宿主机端口，：后为docker的端口
    - 9200作为Http协议，主要用于外部通讯
    - 9300作为Tcp协议，jar之间就是通过tcp协议通讯，包括es集群通讯
- -v: 文件映射
    - ：前为宿主机目录，：后为docker目录
    - 记得给宿主机目录开通权限 (```$ chmod 777  /data/docker/es/data```)否则es将无权写操作而启动失败
- -d：启动docker的版本
- -e：传入的环境变量参数
#### 验证是否启动成功
访问127.0.0.1:9200，若出现以下信息，则启动成功
```
{
name: "da16d189c173",
cluster_name: "docker-cluster",
cluster_uuid: "BbB0h-6BQAqI-gvT8KLKvA",
version: {
    number: "7.5.1",
    build_flavor: "default",
    build_type: "docker",
    build_hash: "3ae9ac9a93c95bd0cdc054951cf95d88e1e18d96",
    build_date: "2019-12-16T22:57:37.835892Z",
    build_snapshot: false,
    lucene_version: "8.3.0",
    minimum_wire_compatibility_version: "6.8.0",
    minimum_index_compatibility_version: "6.0.0-beta1"
    },
tagline: "You Know, for Search"
}
```
> 服务端记得开放9200端口

#### 配置跨域
1. 进入容器
```
$ docker exec -it es /bin/bash
```
- es为容器名称
2. 修改 elasticsearch.yml
```
$ cd config
$ vi elasticsearch.yml
#加入单例信息
discovery.type：      single-node
#加入跨域信息
http.cors.enabled: true
http.cors.allow-origin: "*"
#http.cors.allow-headers: Authorization
#开启用户认证
#xpack.security.enabled: true
#xpack.security.transport.ssl.enabled: true
```
3. 保存并退出
```
$ exit
```
4. 重启es容器
```
$ docker restart es 
```
#### 修改密码
1. 进入容器内
```
$ docker exec -it [es CONTAINER ID] /bin/bash
```
2. 启动修改密码设置
```
$ ./bin/elasticsearch‐setup‐passwords interactive
```
出现以下信息，并按步修改密码
```
You will be prompted to enter passwords as the process progresses.
Please confirm that you would like to continue [y/N]y

Enter password for [elastic]: 
Reenter password for [elastic]: 
Enter password for [apm_system]: 
Reenter password for [apm_system]: 
Enter password for [kibana]: 
Reenter password for [kibana]: 
Enter password for [logstash_system]: 
Reenter password for [logstash_system]: 
Enter password for [beats_system]: 
Reenter password for [beats_system]: 
Enter password for [remote_monitoring_user]: 
Reenter password for [remote_monitoring_user]: 
Changed password for user [apm_system]
Changed password for user [kibana]
Changed password for user [logstash_system]
Changed password for user [beats_system]
Changed password for user [remote_monitoring_user]
```
3. 重启容器

### 安装

## 安装ElasticSearch-Head
安装ElasticSearch-Head，为了有管理界面进行查看ElasticSearch相关信息
#### 拉取ElasticSearch-Head Docker镜像
```
$ docker pull mobz/elasticsearch-head:5
```
#### 启动Es-Head
```
$ docker run -d --name es_admin -p 9100:9100 mobz/elasticsearch-head:5
```
#### 测试访问
访问127.0.0.1:9100，看是否能进入es管理端
- 设置密码时,带用户名和密码访问：127.0.0.1:9100/?auth_user=elastic&auth_password=changeme

## 安装 Kibana
#### 暂先启动容器
```
$ docker run --name es_kibana -p 5601:5601 -d kibana:7.5.1
```
#### 移动配置
1. 查看容器id
```
$ docker ps
```
找到对应的 CONTAINER ID  
2. 新建目录并移动配置
```
$ mkdir /data/docker/kibana/config
#copy docker目录
$ docker cp [CONTAINER ID]:/usr/share/kibana/config/* /data/docker/kibana/config/
```
3. 修改配置
```
$ vim /data/docker/kibana/config/kibana.yml

# 按照以下配置
server.name: kibana
# 允许所有地址访问
server.host: "0.0.0.0"
# elasticsearch的地址(内网地址)
elasticsearch.url: http://xx.xx.xx.xxx:9200
xpack.monitoring.ui.container.elasticsearch.enabled: true
#配置用户密码
#elasticsearch.username: "elastic"
#elasticsearch.password: "changeme"
```
#### 重启容器
1. 删除原有容器
```
#停止原有容器
$ docker stop [CONTAINER ID]
#删除原有容器
$ docker rm [CONTAINER ID]
```
2. 启动新容器
```
$ docker run --name es_kibana -p 5601:5601 -v /data/docker/kibana/config:/usr/share/kibana/config -d kibana:7.5.1

```
#### 测试访问
访问 127.0.0.1:5601  
- 注意建立默认索引

## 安装 Logstash
#### 创建启动容器
```
$ docker run --name es_logstash -d logstash:7.5.1
```

#### 移动配置信息
1. 查看容器id
2. 新建目录并移动配置
```
# 创建用于存放配置的目录
mkdir  /data/docker/logstash
# 复制配置文件，冒号前面为容器ID
docker cp d7405af81c00:/usr/share/logstash/config /data/docker/logstash/config/
# 复制logstash管道文件
docker cp d7405af81c00:/usr/share/logstash/pipeline /data/docker/logstash/pipeline
```
> 记得给文件夹赋予权限

3. 配置配置文件
- 修改logstash.yml
```
$ vim /data/docker/logstash/config/logstash.yml
#修改以下配置
http.host: "0.0.0.0"
#内网ip
xpack.monitoring.elasticsearch.url: http://xxx.xxx.xxx.xxx:9200
#以下开启用户名密码
#xpack.monitoring.elasticsearch.username: "elastic"
#xpack.monitoring.elasticsearch.password: "changme"
```
> 注意：xpack.monitoring.elasticsearch.url一定是本机ip地址
- 修改logstash.conf
```
$ vim /data/docker/logstash/pipeline/logstash.conf
#修改成以下部分
#定义输入部分
input {
    file {
        codec=> json
        path => "/usr/local/*.json"
    }
}
#定义过滤格式
filter {
  #定义数据的格式
  grok {
    match => { "message" => "%{DATA:timestamp}\|%{IP:serverIp}\|%{IP:clientIp}\|%{DATA:logSource}\|%{DATA:userId}\|%{DATA:reqUrl}\|%{DATA:reqUri}\|%{DATA:refer}\|%{DATA:device}\|%{DATA:textDuring}\|%{DATA:duringTime:int}\|\|"}
  }
}
#定义输出格式
output {
   elasticsearch{
     hosts=> "http://xxx.xxx.xxx.xxx:9200"
     #设置的用户名密码
     #user => "elastic"
     #password => "Landleaf@2020"

   }
}
```
#### 重启容器
1. 删除原有容器
```
#停止原有容器
$ docker stop [CONTAINER ID]
#删除原有容器
$ docker rm [CONTAINER ID]
```
2. 启动新容器
```
$ docker run --name es_logstash -v /data/docker/logstash/config:/usr/share/logstash/config -v /data/docker/logstash/pipeline:/usr/share/logstash/pipeline -d logstash:7.5.1
```

#### Logstash插件安装
Logstash插件安装需要进docker容器内进行安装
```
# 进入容器
$docker exec -it [Logstash CONTAINER ID] bash
# 进入logstash的bin目录
$cd /usr/share/logstash/bin
# 安装插件,我这里的示例是安装的jdbc
$ ./logstash-plugin install logstash-input-jdbc
```

## 总结
至此 Elasticsearch + Kibana + Logstash 已安装完成，要使用插拔式日志收集，需要添加其他组件。