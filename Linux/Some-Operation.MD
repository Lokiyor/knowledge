# Linux的一些服务器指令
## 查看服务器信息


## 压缩/解压
* 压缩
1. 将目录里所有jpg文件打包成tar.jpg 
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