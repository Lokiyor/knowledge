# Docker安装sqlserver
## Ubuntu环境
### 查看docker是否运行
```
$ sudo service docker status
```
- 没有运行 则启动
```
sudo service docker start
```
- pull镜像
```
sudo docker pull microsoft/mssql-server-linux:2017-latest
```
### 运行容器
```
sudo docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' \
   -p 1401:1433 --name sql1 \
   -d microsoft/mssql-server-linux:2017-latest
```

- 解释命令
  - -e 'ACCEPT_EULA=Y' 同意协议
  - -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' 设置你sa的sqlserver密码
  - -p 1401:1433 端口映射
  - --name sql1 容器名字
  - -d microsoft/mssql-server-linux:2017-latest 要运行的镜像
- 查看容器是否在运行
```
$ sudo docker ps -a 
CONTAINER ID        IMAGE                                                             COMMAND                  CREATED             STATUS                   PORTS                    NAMES
bff7378d26e4        microsoft/mssql-server-linux:2017-latest                          "/bin/sh -c /opt/mss…"   36 seconds ago      Up 35 seconds            0.0.0.0:1401->1433/tcp   sql1
```
### 登录
```
sqlcmd -S 192.168.0.102,1401 -U SA -p
```
- 注意映射的主机端口不是默认的1433，是1401，所以要指定端口登录