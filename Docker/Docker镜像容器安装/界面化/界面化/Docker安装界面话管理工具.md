# Docker安装界面化管理工具
## Docker安装Portainer
#### 从官网拉拉取相关镜像
```
$ docker pull portainer/portainer:latest
```
#### 建立目录
1. 方式一: docker创建目录
```
$ docker volume create portainer_data
```
> 所建立的目录在 /var/lib/docker/volumes/portainer_data  下
2. 方式二: 手动创建目录
```
$ mkdir /data/docker/portainer/data
```

#### 汉化(可选)
创建界面化公共目录
```
$ mkdir /data/docker/portainer/public
```
上传文件包到该目录, 文件包: 

#### 启动
```
$ docker run -d -p 6380:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data -v /public:/public --name docker_portainer portainer/portainer
```
#### 初始化Portainer
1. 创建用户  
   浏览器输入[ip]:9000创建用户  
   username: admin  
   password: 12345678  
   点击(Create user)创建用户，登陆Portainer
1. 本地Docker,选择Local  
   点击(Connect)进入主页界面
2. 由于只选了本机,所以主页界面只有本机  
   点击(local)进入docker内


### 