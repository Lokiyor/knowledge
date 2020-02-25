# Nginx安装
## Ubuntu安装Nginx
1. 更新apt及其相关依赖
```
##局部更新
$ apt-get update
##全量更新
$ apt-get upgrade
```
2. 下载及安装nginx
```
$ apt-get install nginx
```
3. 修改配置
```
$ vim /etc/nginx/nginx.conf
```
- 配置相关修改信息：[配置](www.baidu.com)
> 默认nginx页面路径: /var/www/html/index.nginx-debian.html
4. 启动nginx
```
$ service nginx restart
```


## CentOS安装Nginx
