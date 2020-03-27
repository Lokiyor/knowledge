# Docker安装EMQ
### 拉去最近镜像
```
$ docker pull emqx/emqx:latest
```
### 启动emq
```
$  docker run -d --name emqx-test -p 1883:1883 -p 8083:8083 -p 8883:8883 -p 8084:8084 -p 18083:18083 emqx/emqx
```