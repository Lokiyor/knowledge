# Dokcer命令字典

#### 查看docker镜像仓库
- docker官方仓库搜索  
[https://hub.docker.com/](https://hub.docker.com/)
> 搜索需要的镜像，并使用对应版本

#### 查看docker镜像
```
$ docker images
```
REPOSITORY|TAG|IMAGE ID|CREATED|SIZE
---|:--:|--:|--:|---:
elasticsearch|7.5.1|2bd69c322e98|3 weeks ago|779MB
kibana|latest|006eb7921543|20 months ago|389MB

- REPOSITORY: 仓库；
- TAG: 镜像标签，比如 5.7、latest 表示不同的版本信息；
- IMAGE ID: 镜像的 ID（两个 ID 完全相同，是同一个镜像，标签名称）；
- CREATED: 镜像最后的更新时间；
- SIZE: 镜像的大小（优秀的镜像一般体积比较小， alpine 版本为轻量级）；

#### 删除docker镜像
```
$ docker rmi [Image ID]
或者
$ docker rmi [REPOSITORY]:[TAG]
```
#### 启动容器


#### 查看容器
```
$ docker ps -a
```
#### 停止容器
```
$ docker stop [CONTAINER ID] 
```
#### 删除容器
```
$ docker rm  [CONTAINER ID] 
```

#### 进入容器
```
$ docker exec -it [CONTAINER ID/CONTAINER NAME] /bin/bash
##
$ docker exec -it [CONTAINER ID/CONTAINER NAME] /bin/sh
```
#### 退出容器
```
$ exit
```
#### 查看容器信息
```
$ docker inspect [CONTAINER ID]
```


### 以下操作慎用（搭建测试集群的时候可使用）
#### 停用全部运行中的容器
```
$ docker stop $(docker ps -q)
```
#### 删除全部容器
```
$ docker rm $(docker ps -aq)
```
#### 一条命令实现停用并删除容器
```
$ docker stop $(docker ps -q) & docker rm $(docker ps -aq)
```

#### 容器文件拷贝
```
$ docker cp [CONTAINER ID]:/etc/conf/xxxx /data/docker/conf
```

#### 查看容器日志
```
$ docker logs [OPTIONS] [CONTAINER ID]
```
OPTIONS参数:
- -f, --follow          跟踪实时日志
- -t, --timestamps      显示时间戳
- --details             显示更多的信息
- --since string        显示自某个timestamp之后的日志，或相对时间，如42m（即42分钟）
- --tail string         从日志末尾显示多少行日志， 默认是all
- --until string        显示自某个timestamp之前的日志，或相对时间，如42m（即42分钟）

