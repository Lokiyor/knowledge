# KeepAlived安装
## Ubuntu安装
1. 安装KeepAlived
```
$ apt-get install keepalived
```
2. 配置
在目录下配置文件```/etc/keepalived/keepalived.conf```  
具体配置参照： [keepalived配置]()

3. 配置检测nginx脚本
用于轮询检测nginx,若nginx down机,则自关本地keepalived  
脚本详见： [keepalived检测脚本]()

4. 启动keepalived
```
$ service keepalived start
```