# Python 环境搭建
## Python下载
* python官网: [https://www.python.org/](https://www.python.org/)
* 下载地址: [https://www.python.org/downloads/release](https://www.python.org/downloads/release)
* 下载最新版本
1. 32位: Windows x86 executable installer
2. 64位: Windows x86-64 executable installer
## Python 安装
### window版本
* 下载后，双击下载包，进入 Python 安装向导，安装非常简单，你只需要使用默认的设置一直点击"下一步"直到安装完成即可。
## Python 环境变量
* (window向导会默认添加环境变量)

变量名|描述
---|:--:
PYTHONPATH|PYTHONPATH是Python搜索路径，默认我们import的模块都会从PYTHONPATH里面寻找。
PYTHONSTARTUP|Python启动后，先寻找PYTHONSTARTUP环境变量，然后执行此变量指定的文件中的代码。
PYTHONCASEOK|加入PYTHONCASEOK的环境变量, 就会使python导入模块的时候不区分大小写。
PYTHONHOME|另一种模块搜索路径。它通常内嵌于的PYTHONSTARTUP或PYTHONPATH目录中，使得两个模块库更容易切换。
## 运行Python
### 检验python
#### window版本
* 打开cmd, 打印python,显示python版本后及表示安装成功