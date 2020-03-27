# Docker安装部署
## 前提条件
- 目前，CentOS 仅发行版本中的内核支持 Docker。
- Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。
- Docker 运行在 CentOS-6.5 或更高的版本的 CentOS 上，要求系统为64位、系统内核版本为 2.6.32-431 或者更高版本。
##### 查看CentOS内核版本
```
$ uname -r
```
##### 查看docker版本，验证是否安装
```
$ docker version
```
## CentOS 安装
#### 准备工作
1. 安装wget
```
$ yum install -y wget
```
2. 关闭防火墙（如果需要关闭的话）
```
$ systemctl stop firewalld
$ systemctl disable firewalld
```

#### 安装Docker
1. 移除旧的版本
```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
2. 获取官方源
```
$ wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo
```
3. 安装docker-ce
```
yum install -y docker-ce
```
- 当然也可以选择安装指定版本（用下面的命令可以查看可以安装的版本）
```
yum list docker-ce --showduplicates | sort -r
```
- 比方想安装docker ce的版本18.03.0
```
yum install -y docker-ce-18.03.0.ce-1.el7.centos
```
4. 设置镜像加速器
- 国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器，国内很多云服务商都提供了国内加速器服务
  - 阿里镜像加速器（需要阿里云账户，支付宝登陆），阿里云镜像控制台：[https://cr.console.aliyun.com/cn-hangzhou/mirrors](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
  - Azure 中国镜像：[https://dockerhub.azk8s.cn](https://dockerhub.azk8s.cn)
  - 七牛云加速器：[https://reg-mirror.qiniu.com](https://reg-mirror.qiniu.com)
- 在 /etc/docker/daemon.json 中写入下面内容,没有的话，则创建文件
```
{
    "registry-mirrors":[
        "https://dockerhub.azk8s.cn",
        "https://reg-mirror.qiniu.com"
    ]
}
```
- 重启服务(已经启动服务之后)
```
$ systemctl daemon-reload
$ systemctl restart docker
```
#### 设置启动/开机启动 
```
$ systemctl start docker
$ systemctl enable docker
```


## Ubuntu 安装
#### 移除旧版本
```
$ sudo apt-get remove docker docker-engine docker-ce docker.io
```
#### 更新apt包索引
```
$ sudo apt-get update
```
#### 安装以下包以使apt可以通过HTTPS使用存储库（repository）
```
$ sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
```
#### 添加Docker官方的GPG密钥
```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
#### 使用下面的命令来设置stable存储库
```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
#### 安装最新版本的Docker CE
```
$ sudo apt-get install -y docker-ce
```

#### 安装其他版本（可选）
- 在生产系统上，可能会需要应该安装一个特定版本的Docker CE，而不是总是使用最新版本
```
$ apt-cache madison docker-ce
```
安装特定版本
```
$ sudo apt-get install docker-ce=<VERSION>
```
#### 同上（设置镜像地址）
#### 设置启动和开机启动