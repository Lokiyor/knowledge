# Docker目录迁移
之前在服务器重启docker容器的时候，提示```/var/lib/docker/overlay2 no space left on device```，可知```var/lib/docker```目录下占用的空间太多，下面是具体操作

#### 查看docker的磁盘占用情况
```
$ docker system df
```

#### 清理docker(看情况使用，否则会误删)
```
#清理磁盘，删除关闭的容器、无用的数据卷和网络，以及dangling镜像(即无tag的镜像)
$ docker system prune
#清理得更加彻底，可以将没有容器使用Docker镜像都删掉
$ docker system prune -a
```
> 会清理 没有开启的  Docker 镜像  
> 建议慎用

#### 迁移目录
1. 停止docker
```
$ systemctl stop docker
```
2. 创建新的docker目录（建议找大一点的磁盘）
```
$ mkdir /data/docker/lib
```
3. 迁移目录下所有文件
```
$ rsync -avz /var/lib/docker /data/docker/lib/
```
等待迁移完成

4. 配置devicemapper.conf文件
看是否存在，不存在，则新建
```
$ mkdir -p /etc/systemd/system/docker.service.d/
$ vi /etc/systemd/system/docker.service.d/devicemapper.conf
```
 配置写入
```
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd  --graph=/data/docker/lib/docker
```
#### 重新加载docker
```
$ systemctl daemon-reload
$ systemctl restart docker
$ systemctl enable docker
```
#### 查看是否配置成功
```
$ docker info
```
查看目录是否成功  
并查看镜像和容器是否还在
```
$ docker images
$ docker ps -a
```
#### 删除源目录文件
确认无误后，删除目标文件夹
```
$ rm -rf /var/lib/docker/
```