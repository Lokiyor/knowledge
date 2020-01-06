# Frp内网穿透的使用
## Frp基本
内网穿透的高性能的反向代理应用，支持 tcp, udp 协议，为 http 和 https 应用协议提供了额外的能力  
分为 服务端frps和客户端frpc  
官方下载地址: [https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)
> 下载较慢，请多试几次


### Frp服务端
服务端 frps.ini 配置
```
[common]
# 设置连接端口
bind_port = 7000

# 设置dashboard服务登陆端口
dashboard_port = 7500
# 设置dashboard登录信息
dashboard_user = landleaf
dashboard_pwd = landleaf@2019

# 设置日志显示级别{debug, info, warn, error)
log_level = info
# 设置日志最大天数
log_max_days = 5

# 设置特权模式是否开启，开通后web,ssh等使用都可以直接在客户端设置
privilege_mode = true
# 设置特权模式token值
privilege_token = landleaf@2019

# 设置转发端口(http转发端口只以最后一个为主)
vhost_http_port = 7200
```
- 启动frp服务端
```
$ nohup ./frps -c frps.ini &
```
### Frp客户端
客户端 frpc.ini 配置
```
[common]
# 服务器公网ip
server_addr = xx.xx.xx.xxx
# 服务器设置的端口(和frps bind_port相同)
server_port = 7000

# 默认设置
admin_addr = 127.0.0.1
# 本地管理端口，自行修改
admin_port = 7400

# for privilege mode，和服务端一致
privilege_token = 123456

#以下为对应本地开放的应用端口
[web01]
type = tcp
# 本地应用的端口
local_port = 9097
# 外网访问的地址(可以通过域名和子域名绑定)
custom_domains = 40.73.71.124
# 绑定服务端外网访问的端口
remote_port = 9097

[web02]
type = tcp
local_port = 9098
custom_domains = 40.73.71.124
remote_port = 9098


[http01]
type = http
local_port = 9099
# 通过服务端http转发,通过子域名区分
custom_domains = ps.lokiy66.com
```
- window启动frp客户端
```
> ./frpc.exe -c ./frpc.ini
```
- 注册为window服务  
参照: [https://github.com/Lokiyor/knowledge/blob/master/Other/Window%E6%9C%8D%E5%8A%A1/Window%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C.md](https://github.com/Lokiyor/knowledge/blob/master/Other/Window%E6%9C%8D%E5%8A%A1/Window%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C.md)