# Logstash使用详情
## 日志修改
#### 日志替换Logstash 的@timestamp
1. 在 logstash.conf 中添加以下内容
```
filter{
    grok{
        match => {"message" => "(?<logdate>\d{14})"}
    }
    date{
        match => ["logdate", "yyyyMMddHHmmss"]
        locale => "en"
        add_tag => "@timestamp"
        timezone => "Asia/Shanghai"
     }
    mutate{
        remove_field => ["logdate"]
    }
}

```
- logdate: 为后面正则匹配到的数据替换成的变量
- @timestamp: 为logstash中的时间

2. 重启Logstash容器
```
$ docker restart [Logstash CONTAINER ID]
```