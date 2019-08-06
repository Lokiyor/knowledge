# Yapi安装教程
* 近日公司新项目开发，需要为Android、IOS和前端提供接口文档，故想到Yapi接口文档管理平台。
## 环境准备
* 操作系统: CentOS 7
* 环境要求:
    + NodeJS > 7.6
    + MongoDB > 2.6
    + git
### 安装NodeJS
[链接](链接)
### 安装MongoDB
[链接](链接)
### 安装Git
[链接](链接)
## 搭建YApi环境
### 搭建YApi
1. 准备环境搭建完成后，开始搭建YApi，安装命令
```
$ npm install -g yapi-cli --registry https://registry.npm.taobao.org

/usr/bin/yapi -> /usr/lib/nodejs/yapi-cli/bin/yapi-cli
/usr/bin/yapi-cli -> /usr/lib/nodejs/yapi-cli/bin/yapi-cli
+ yapi-cli@1.2.8
added 256 packages from 129 contributors in 9.321s

$ yapi server

在浏览器打开 http://0.0.0.0:9090 访问。非本地服务器，请将 0.0.0.0 换成指定的域名或ip
```
* 假如yapi指令找不到,则进到第一指令安装包目录/usr/bin 执行 yapi server 命令
* 记得服务器打开9090端口
2. 执行yapi可视化部署程序
* 部署版本: 1.8 (自行选择)
* 公司名称: Lokiy (随便填)
* 部署路径: /usr/yapi (选择服务器上建立的yapi目录)
* 管理员邮箱: XXXX@XXX.com (设置的初始化管理员邮箱)
* 网站端口: 3000 (默认3000端口，记得打开防火墙)
* 数据库地址: 127.0.0.1 (配置mongodb服务器的地址)
* 数据库端口: 27017 (配置mongodb服务的端口)
* 数据库名: yapi (yapi使用的mongodb数据库名，需提前建立)
* 数据库认证: (可选)
    * 用户名: aaa
    * 密码: 123
3. 根据上述部署信息，点击【开始部署】，进行部署任务
* 可以看到页面的部署信息，后台服务器也能看到相应信息
* 待部署日志显示
```
初始化管理员帐号成功:"XXXX@XXX.com",密码:"ymfe.org"
部署成功，请切换到部署目录，输入:"node vendor/server/app.js" 指令启动服务器，然后再浏览器中打开:http//127.0.0.1:3000 访问 
```
* 部署时遇到的一个小问题
    * 部署报错，显示信息:
    ```
    ...
    >  node server/install.js
    Error:  internal/modules/cjs/loader.js:582
        throw err;
        ^
    Error: Cannot find module 'json-schema-faker'
        at Function.Module._resolveFilename (internal/modules/cjs/loader.js:580:15)
    ...
    ```
    * 原因: nodejs 权限问题
    * 解决方案: 更改nodejs目录下的权限
    ```
    $ chown -R root:root /nodejs (nodejs安装目录)
    ```
4. 根据部署日志的信息，启动服务器
* 在yapi目录下启动服务: node vendors/server/app.js
    * 后台启动: nohup (上指令) & 