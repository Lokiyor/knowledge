# Window服务注册
### window服务注册
#### 准备srvany.exe，instsrv.exe两个文件
1. 链接：[https://pan.baidu.com/s/19FfFoN0kSvqBpr8PRtocEw](https://pan.baidu.com/s/19FfFoN0kSvqBpr8PRtocEw)  
提取码：duba  
2. 下载之后，将srvany.exe，instsrv.exe文件放在C:\Windows\System32路径下面
> 64位电脑文件目录为 C:\Windows\SysWOW64 下面

#### 注册服务
管理员打开cmd, 输入下面命令，注册名为ServerName的服务
```
C:\Windows\SysWOW64\instsrv.exe ServiceName C:\Windows\SysWOW64\srvany.exe
```

#### 打开注册表，修改服务信息
1. 按win+R键打开运行，输入regedit，打开注册表
2. 进入注册表在HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services下找到刚刚注册的服务名ServiceName
3. 在ServiceName新建一个项，名称为“Parameters”的项
4. 在Parameters中新建以下字符串:  
   
名称|类型|描述
---|:--:|---:
AppDirectory|REG_SZ|运行目录（可选）
Application|REG_SZ|运行exe路径
AppParameters|REG_SZ|运行exe参数
Description|REG_SZ|服务描述

> 上述参数中碰到文件目录的  单斜杠\ 要变为双斜杠 \\\   
> 否则开机启动会无效

#### window服务中启动
1. 按win+R键打开运行，输入Services.msc,进入服务列表
2. 查找名为ServerName的服务，点击启动开启服务


### window服务删除
1. 停止名为 ServerName的服务
2. 执行以下指令移除
```
C:\Windows\SysWOW64\instsrv.exe ServiceName remove
```