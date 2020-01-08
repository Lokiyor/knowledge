# JAVA-安装
## 安装JAVA JDK
### 一、查看现有JAVA环境
- 查看CentOS自带的JAVA环境
```
yum list installed |grep java
```
### 二、卸载现有JAVA环境
```
yum -y remove java-1.8.0-openjdk*
yum -y remove tzdata-java*
```
### 三、查看yum库中的JAVA安装包
```
yum -y list java*
```
### 四、安装JAVA JDK
```
yum -y install java-1.8.0-openjdk*
```
### 五、查找JAVA安装路径
```
> which java

> ls -lrt /usr/bin/java  //（也就是上一步查询出来的路径）

> ls -lrt /etc/alternatives/java（也就是上一步查询出来的路径）
```
- 从路径中可以看到在jvm目录下，跳转到jvm的目录
```
> cd /usr/lib/jvm

> ls  //查看安装目录
```
## 配置Java环境变量
### 编辑环境变量
```
vi /etc/profile        //去编辑环境变量
```
- 滚动到最底部，按键盘上的i键切换到编辑模式，输入
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0
 
export JRE_HOME=$JAVA_HOME/jre  
 
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
```
- 按键盘上的Esc键退出编辑模式，输入:wq进行保存并退出
- 使配置马上生效
```
source /etc/profile         //使配置立即生效
```
## 检查JAVA安装和配置情况
- java -version
- javac 





## Ubuntu安装JAVA
1. 更新软件包列表：
```
$ sudo apt-get update
```
2. 安装openjdk-8-jdk：
```
$sudo apt-get install openjdk-8-jdk
```
3. 查看java版本，看看是否安装成功：
```
$ java -version
```