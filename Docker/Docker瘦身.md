# Docker瘦身
> 在公司宿主机上运行的docker，时间长了以后会发现，占了宿主机大量磁盘，在此记录一下清理过程
## 清理容器日志
导致日志大，是因为docker未设置相应的日志配置
### 配置日志参数


## 清除相关
### 清除无用卷
#### 列出无用的卷
```
$ docker volume ls -qf dangling=true
```
#### 清理无用的卷
```
$ docker volume rm $(docker volume ls -qf dangling=true)
```