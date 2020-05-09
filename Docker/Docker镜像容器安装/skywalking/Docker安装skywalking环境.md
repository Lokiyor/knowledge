# Docker安装skywalking环境
- 此次安装版为es6,之前安装es7的时候，索引类型导致skywalking启动不了
## 安装云端收集组件
直接用docker-compose统一部署，skywalking-aop/skywalking-ui/elasticsearch  
1. 配置在同目录下[docker-compose-skywalking.yaml]()
2. 在配置所在目录下直接启动
```
$ docker-compose -f docker-compose-skywalking.yaml up -d
```
3. 访问 127.0.0.1:18080 看是否能正常访问skywalking

## 安装收集探针
#### 下载skywalking探针
[http://skywalking.apache.org/downloads/](http://skywalking.apache.org/downloads/)  
下载 Binary Distribution
#### 上传至服务所在服务器的相应位置并解压
#### 修改配置并修改服务启动参数
##### 修改配置
找到skywalking-agent.jar目录下（在解压文件的agent目录下），进入config目录，修改agent.config  
```
collector.backend_service=${SW_AGENT_COLLECTOR_BACKEND_SERVICES:xxx.xxx.xxx.xxx:11800}

```
修改上述skywalking-oap的服务地址，其他参数视情况修改
##### 修改服务启动参数
在服务启动脚本，修改启动参数，加上以下参数
```
$ java -javaagent:/home/skywalking/apache-skywalking-apm-bin-es7/agent/skywalking-agent.jar -Dskywalking.agent.service_name=xxx -jar xxx.jar
```
重启服务，即可登陆skywalking查看到调用链路