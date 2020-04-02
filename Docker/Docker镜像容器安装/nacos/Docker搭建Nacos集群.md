# Docker搭建Nacos集群
## 安装mysql，及初始化
### 首先得准备mysql
> 这里mysql5.7和mysql8 官方好像都支持，这里使用得是 docker启动得mysql5.7

### 初始化数据
数据脚本来源[nacos-db.sql](https://github.com/alibaba/nacos/blob/develop/config/src/main/resources/META-INF/nacos-db.sql)

## Docker搭建Nacos
[Nacos官方文档](https://nacos.io/zh-cn/docs/quick-start-docker.html)

### 前置操作
#### 拉取最新docker nacos-server镜像
```
$ docker pull nacos/nacos-server
```
#### 外挂相关目录
```
$ mkdir -p /data/docker/nacos/logs
$ mkdir -p /data/docker/nacos/init.d
```
#### 配置custom.properties
1. 下载配置文件[custom.properties](https://github.com/nacos-group/nacos-docker/blob/master/example/init.d/custom.properties)  
2. 上传至每台集群服务器配置的 init.d/ 目录下
> 此项配置 ```management.endpoints.web.exposure.include=*``` 打开了prometheus的nacos-monitor监控信息，为之后nacos监控所用
#### 配置docker-compose启动文件
每台服务器配置不同的 docker-compose-nacos.yaml 文件，放在 /data/docker/nacos/ 目录下  
- docker-compose-nacos.yaml 文件在同级目录下，每台宿主机根据自身情况修改相应ip
> 此处有坑，哈哈哈哈，坑点参考[移除数据库主从镜像配置](https://github.com/nacos-group/nacos-docker/wiki/%E7%A7%BB%E9%99%A4%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%BB%E4%BB%8E%E9%95%9C%E5%83%8F%E9%85%8D%E7%BD%AE)
> 由于nacos官方文档没有更新此项，而在github上更新说明，需注意

#### 启动docker-nacos
在不同服务器上，分别启动docker-nacos  
在docker-compose-nacos.yaml所在的文件夹下启动
```
$ docker-compose -f docker-compose-nacos.yaml up -d
```

### 验证
#### 登陆nacos
1. 登陆 http://xxx.xxx.xxx.xxx:8848/nacos 默认: nacos/nacos
2. 在ClusterManagement-Cluster Node List 中，看是否存在多个nacos，其中一个为LEADER，其他为FOLLOWER，表示搭建成功
#### 查看是否能看到监控信息
访问 http://xxx.xxx.xxx.xxx:8848/nacos/actuator/prometheus  
若能访问到相关监控信息，则监控信息已收集，为后续prometheus做铺垫

### 扩展
#### JAVA端配置
```
spring:
  cloud:
    nacos:
      discovery:
        server-addr: xxx.xxx.xxx.xxx:8848,xxx.xxx.xxx.xxx:8848 ###此处配置多个集群的nacos即可
```
> 若想server-addr配置成一个ip端口，则可使用nginx/slb等其他方式进行反向代理，此处不做延申