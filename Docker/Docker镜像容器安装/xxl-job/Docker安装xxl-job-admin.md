# Docker安装xxl-job-admin
## 拉取最新镜像
```
$ docker pull xuxueli/xxl-job-admin:2.1.2
```
## 配置日志目录
```
$ mkdir -p /data/docker/xxl-job/applogs
$ chmod 777 applogs
```
## 启动调度中心
```
$ docker run -e PARAMS="--spring.datasource.url=jdbc:mysql://xxx.xxx.xxx.xxx:3306/xxl_job?Unicode=true&characterEncoding=UTF-8 --spring.datasource.username=root  --spring.datasource.password=123456" -p 8080:8080 -v /data/docker/xxl-job/applogs:/data/applogs --name xxl-job-admin  --privileged=true -d xuxueli/xxl-job-admin:2.1.2
```
> 对应需要加的环境参数，在PARAMS中 继续用-- 添加
## 访问调度中心
访问 xxx.xxx.xxx.xxx:8080/xxl-job-admin   
默认登录名/密码：admin/123456