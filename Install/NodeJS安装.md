# NodeJS安装
## Linux版本
### 直接使用已编译好的包
1. 直接下载已编译好的包,解压后直接使用
```
$ wget https://nodejs.org/dist/v10.16.1/node-v10.16.1-linux-x64.tar.gz
$ tar -zxvf node-v10.16.1-linux-x64.tar.xz
$ cd node-v10.16.1-linux-x64/
```
2. 用 ln 命令来设置软连接来设置全局配置
```
$ ln -s /usr/software/nodejs/bin/npm   /usr/local/bin/ 
$ ln -s /usr/software/nodejs/bin/node   /usr/local/bin/
```
3. 查看nodeJs版本看是否安装成功
```
$ node -v
v10.16.1
```
## Window版本