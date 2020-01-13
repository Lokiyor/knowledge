# ELK拓展安装
基于上期[Docker安装ELK](https://github.com/Lokiyor/knowledge/blob/master/Middleware/ELK/Docker%E5%AE%89%E8%A3%85ELK.md)之后，继续安装其他组件，用于插拔式收集日志，对代码和其他无任何倾入

## 安装日志消息转发中间件
### 安装redis中间件
参考[Dokcer安装redis](https://github.com/Lokiyor/knowledge/blob/master/Docker/Docker%E9%95%9C%E5%83%8F%E5%AE%B9%E5%99%A8%E5%AE%89%E8%A3%85/Docker%E5%AE%89%E8%A3%85%E5%8D%95%E6%9C%BA%E7%89%88Redis.md)安装详情

### (或)安装Kafka中间件


## 安装Filebeat
安装日志收集中间件filebeat
#### 下载安装包
安装包地址：[https://www.elastic.co/cn/downloads/beats/filebeat](https://www.elastic.co/cn/downloads/beats/filebeat)

#### 上传并解压安装包
上传安装包到需要收集日志的服务器
```
$ tar -zxvf filebeat-7.5.1-linux-x86_64.tar.gz
```
#### 修改配置文件
修改fiblebeat.yml的input和output信息
```
# 日志来源
filebeat.inputs:

#单个日志来源
- type: log
  enabled: true
  #日志路径
  paths:
    - /home/software/aaa/nohup.out
    #额外参数
  fields:
    level: info
    app: aaa
    host: xxx.xxx.xxx.xxx
    env: prod
    #日志收集格式
  #multiline.pattern: ^\[[0‐9]{14}\]
  #multiline.negate: true
  #multiline.match: after

- type: log
  enabled: true
  paths:
    - /home/software/bbb/nohup.out
  fields:
    level: info
    app: bbb
    host: xxx.xxx.xxx.xxx
    env: prod
    #按正常日志时间开头匹配
  multiline.pattern: ^[0-9]{14}
  multiline.negate: true
  multiline.match: after



output.redis:
  # Array of hosts to connect to.
  #输出到redis的机器
   hosts: ["xxx.xxx.xxx.xxx:6379"]   
   password: 123456
   #redis中日志数据的key值ֵ 
   #日志收的key
   key: sh             
   #使用的数据库         
   db: 0
   #超时时间 尽量长一点
   timeout: 60
```

#### 后台启动
后台命令启动
```
$ nohup /usr/local/filebeat/filebeat-7.5.1-linux-x86_64/filebeat -e -c /usr/local/filebeat/config/filebeat.yml &
```

> 推荐使用rpm安装filebeat, 上述不能后台运行


#### rpm安装filebeat
1. 下载远程包
```
$ curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.5.1-x86_64.rpm
```
2. 安装
```
$ rpm -ivh filebeat-7.5.1-x86_64.rpm --force --nodeps
```
3. 配置文件
```
$ vim /etc/filebeat/filebeat.yml
```
4. 启动
```
$ service filebeat start
```

## 编辑修改
#### 修改Logstash 相关配置
1. 修改logstash.conf的输入输出配置
```
input {
  redis {
    host => "xxx.xxx.xxx.xxx"
    port => "6379"
    password => "123456"
    db => "0"
    data_type => "list"
    key => "sh"
  }
}

output {
  elasticsearch {
    hosts => ["xxx.xxx.xxx.xxx:9200"]
    index => "%{[fields][app]}-%{[fields][env]}-%{+YYYY.MM.dd}"
    user => "elastic"
    password => "changeme"
  }
}

```
2. 重启Docker的Logstash容器
```
$ docker restart [Logstash CONTAINER ID]
```