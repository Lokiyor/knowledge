# Docker安装Mysql
#### 下载镜像
```
$ docker pull mysql:latest
```
#### 创建外部文件和目录
1. 创建目录及文件
```
$ touch /data/docker/mysql/conf/my.cnf
$ mkdir /data/docker/mysql/data
$ mkdir /data/docker/mysql/log
$ mkdir /data/docker/mysql/mysql-files
```
2. 编辑文件及设置目录权限
```
$ chmod 777 /data/docker/mysql/data
$ chmod 777 /data/docker/mysql/log
$ chmod 777 /data/docker/mysql/mysql-files
$ vim chmod 777 /data/docker/conf/my.cnf

[mysqld]
user=mysql
character-set-server=utf8mb4
default_authentication_plugin=mysql_native_password

[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4
```

#### 启动mysql
```
$ docker run -p 3306:3306 --name docker_mysql -v /data/docker/mysql/conf/my.cnf:/etc/mysql/my.cnf -v /data/docker/mysql/log:/var/log -v /data/docker/mysql/data:/var/lib/mysql -v /data/docker/mysql/mysql-files:/var/lib/mysql-files/ -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```
