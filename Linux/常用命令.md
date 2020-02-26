# Linux的一些服务器指令
## 基本操作
1. 清空某个文件
```
echo "" > XXX.log
```
2. 拷贝 tomcatHr下所有子文件 为tomcatCe
```
cp -r tomcatHr tomcatCe
```

## 查看服务器信息
1. 查看CPU信息
```
cat /proc/cpuinfo
```
2. 查看物理CPU的个数:
```
cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
```
3. 查看逻辑CPU的个数:
```
cat /proc/cpuinfo |grep "processor"|wc -l
```
4. 查看CPU是几核:
```
cat /proc/cpuinfo |grep "cores"|uniq
```
5. 查看磁盘:
```
ps aux|grep 文件
```

## 压缩/解压
### 安装压缩解压软件
```
yum -y install lrzsz
```
* 压缩
1. 将目录里所有jpg文件打包成jpg.tar
``` 
tar -cvf jpg.tar *.jpg
```
2. 将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
```
tar -czf jpg.tar.gz *.jpg
``` 
3. 将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
```
tar -cjf jpg.tar.bz2 *.jpg
```
4. 将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
```
tar -cZf jpg.tar.Z *.jpg
```
5. rar格式的压缩，需要先下载rar for linux
```
rar a jpg.rar *.jpg
```
6. zip格式的压缩，需要先下载zip for linux
```
zip jpg.zip *.jpg
```
* 解压
1. 解压 tar包
```
tar -xvf file.tar
```
2. 解压tar.gz
```
tar -xzvf file.tar.gz
```
3. 解压 tar.bz2
```
tar -xjvf file.tar.bz2
```
4. 解压tar.Z
```
tar -xZvf file.tar.Z
```
5. 解压rar
```
unrar e file.rar
```
6. 解压zip
```
unzip file.zip
```