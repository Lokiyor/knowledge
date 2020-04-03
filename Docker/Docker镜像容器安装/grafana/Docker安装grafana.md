# Docker安装grafana
此处也有个坑，如果只拉取grafana/grafana，并通过容器启动，并不能正常访问grafana,从官网文档看，docker的启动还需要拉取并启动grafana/grafana-image-renderer
### 安装
#### 拉取最新镜像
```
$ docker pull grafana/grafana
$ grafana/grafana-image-renderer
```
#### 配置启动文件
```
 $ mkdir -p /data/docker/grafana
 $ touch docker-compose-grafana.yml
 version: '2'

services:
  grafana:
    image: grafana/grafana:latest
    ports:
     - "3000:3000"
    environment:
      GF_RENDERING_SERVER_URL: http://renderer:8081/render
      GF_RENDERING_CALLBACK_URL: http://grafana:3000/
      GF_LOG_FILTERS: rendering:debug
   # 看需求配置
   # volumes:
   #  - /data/docker/grafana/grafana-storage:/var/lib/grafana #grafana仓库
  renderer:
    image: grafana/grafana-image-renderer:latest
    ports:
      - 8081
```
##### 外挂目录(看需求配)
```
$ mkdir /data/docker/grafana/grafana-storage
$ chmod 777 -R /data/docker/grafana/grafana-storage
```
#### 启动
进入docker-compose-grafana.yml相应的目录下，并启动
```
$ docker-compose -f docker-compose-grafana.yml up -d
```
### 验证使用
登陆 xxx.xxx.xxx.xxx:3000 看是否能正常访问
默认用户名密码: admin/admin

- Configuration 中配置数据来源
- Create-import 中导入仪表盘模板，可根据id来，可导入json格式