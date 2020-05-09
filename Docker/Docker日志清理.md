# Docker日志清理
最近公司Docker跑了段时间，发现容器日志打了很多，所以做了下docker日志的清理
## 临时处理
##### 通过脚本找出docker日志的大小
```
#!/bin/sh
 
echo "======== docker containers logs file size ========"
 
logs=$(find /data/docker/lib/docker/containers/ -name *-json.log)
 
for log in $logs
do
ls -lh $log
done
```
运行上面脚本可以查看到所有容器的日志情况
##### 清理日志脚本
```
#!/bin/sh
 
echo "======== start clean docker containers logs ========"
 
logs=$(find /var/lib/docker/containers/ -name *-json.log)
 
for log in $logs
do
echo "clean logs : $log"
cat /dev/null > $log
done
 
echo "======== end clean docker containers logs ========"
```
以上脚本可以把日志全部清空

## 永久处理办法
##### 修改docker配置文件
修改 /etc/docker/daemon.json 文件，添加日志大小和切割日志数量，添加以下配置
```
{
  "log-driver": "json-file",
  "log-opts": {"max-size": "10m", "max-file": "3"}
}
```
##### 重启docker 
```
$ service docker restart
```
##### 重启所有容器
```
$ docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
```