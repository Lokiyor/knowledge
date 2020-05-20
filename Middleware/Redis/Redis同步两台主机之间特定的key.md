# Redis同步两台主机之间特定的key
在有redis-cli的服务器上编辑一下脚本
```
#!/bin/bash
#源地址
src_ip=xxx.xxx.xxx.xxx
src_port=6379
src_db=14
src_pw='123456'

#目标地址
dest_ip=xxx.xxx.xxx.xxx
dest_port=6379
dest_db=14
desc_pw='123456'

/usr/bin/redis-cli -h $src_ip -p $src_port -a $src_pw -n $src_db keys "*" | while read key
do
        /usr/bin/redis-cli -h $src_ip -p $src_port -a $src_pw  -n $src_db --raw dump $key | perl -pe 'chomp if eof' | /usr/bin/redis-cli -h $dest_ip -p $dest_port -a $desc_pw -n $dest_db -x restore $key 0
        echo "migrate key $key"
done
```
