# MongoDB 安装
## Windows 版本安装
### 下载安装
* 官方地址：[https://www.mongodb.com/download-center/community](https://www.mongodb.com/download-center/community)
### 

## Linux 版本安装
### Ubuntu 安装mongodb
1. 在终端输入GPK码
```
$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
```
2. 添加mongoDB源
* Ubuntu 18.04 LTS:
```
$ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```
* Ubuntu 16.04 LTS:
```
$ echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/4.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb.list
```
3. 安装
```
$ sudo apt update
$ sudo apt install mongodb
```
也可以直接安装指定版本
```
$ sudo apt install mongodb-org=4.0.1 mongodb-org-server=4.0.1 mongodb-org-shell=4.0.1 mongodb-org-mongos=4.0.1 mongodb-org-tools=4.0.1
```
4. 对mongodb进行配置
* 若安装好在相应目录下又conf配置，则直接修改
```
$ touch /etc/mongodb.conf
$ vim /etc/mongodb.conf

#数据库数据存放目录
dbpath=/var/lib/mongodb/
#数据库日志存放目录
logpath=/var/log/mongodb/mongodb.log
#以追加的方式记录日志
logappend = true
#端口号 默认为27017
port=27017 
#以后台方式运行进程
fork=true 
#开启用户认证
auth=true
#关闭http接口，默认关闭http端口访问
#nohttpinterface=true
#mongodb所绑定的ip地址
#bind_ip = 0.0.0.0
#启用日志文件，默认启用
journal=true 
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=true
```
5. 启动服务
* 进入相应目录，之后启动服务
* /usr/bin/mongo 
* /usr/bin/mongod
```
$ ./mongod --config /etc/mongodb.conf
```
### CentOS 安装mongodb

## 可视化界面安装
### Robo 3T 安装
* 下载地址：[https://robomongo.org/download](https://robomongo.org/download)