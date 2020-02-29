# Docker安装单机Mongo
#### 拉取镜像
```
$ docker pull mongo:latest
```
#### 创建mongo外挂配置和目录
```
$ mkdir /data/docker/mongo/db
$ mkdir /data/docker/mongo/config
```
#### 启动mongo
权限认证启动mongo
```
$ docker run -p 27017:27017 --name docker_mongo -v /data/docker/mongo/db:/data/db -d mongo --auth
```
#### 进入mongo创建用户
1. 以admin用户进入mongo
```
$  docker exec -it [Mongo CONTAINER ID] mongo admin
```
2. 创建管理员帐号
```
> db.createUser({ user:'root',pwd:'123456',roles:[ { role:'root', db: 'admin'}]});
```
3. 验证创建的用户
```
> db.auth("root","123456");
1
>
```

#### 使用客户端连接mongo
- 下载Robo 3T
> 对于Mongo 4.0+ , Robo 3T 1.2+不支持,需要下载1.3+
- 或下载Studio 3T
