# docker-compose的使用
### 配置

### 启动
#### 若命名为 docker-compose.yml 文件
在当前目录下可以直接使用以下命令启动
```
$ docker-compose up -d
```
#### 若命名为其他，例如 docker-compose-mysql.yaml
则使用以下命令启动
```
$ docker-compose -f docker-compose-mysql.yaml up -d
```