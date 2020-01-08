# Docker安装单机版Redis
#### 获取最新镜像
```
$ docker pull redis:latest
```
#### 获取配置
从官网下载redis配置，并作以下修改   
官网配置地址：[http://download.redis.io/redis-stable/redis.conf](http://download.redis.io/redis-stable/redis.conf)   
修改如下：
- bind 127.0.0.1    #注释掉这部分，这是限制redis只能本地访问
- protected-mode no #默认yes，开启保护模式，限制为本地访问
- daemonize no      #默认no，改为yes意为以守护进程方式启动，可后台运行，除非kill进程，改为yes会使配置文件方式启动redis失败
- databases 16      #数据库个数（可选），我修改了这个只是查看是否生效。。
- dir ./            #输入本地redis数据库存放文件夹（可选）
- appendonly yes    #redis持久化（可选）
- requirepass Landleaf@2020     #设置访问密码

#### 启动Redis
使用以下命令启动redis
```
$ docker run -p 6379:6379 --name es_redis -v /data/docker/redis/config/redis.conf:/etc/redis/redis.conf -v /data/docker/redis/data:/data -d redis redis-server /etc/redis/redis.conf --appendonly yes
```
> 记得对外开放6379端口