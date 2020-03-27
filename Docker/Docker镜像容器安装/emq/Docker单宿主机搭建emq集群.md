# Dokcer单宿主机搭建emq集群
### 配置emq集群
#### 配置docker-compose.yaml文件(static集群)
```
version: '3'

services:
  emqx1:
    image: emqx/emqx
    restart: always
    ports:
      - 18083:18083
      - 1883:1883
    environment:
    - EMQX_NAME=emqx
    - EMQX_HOST=node1.emqx.io
    - EMQX_CLUSTER__DISCOVERY=static
    - EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io, emqx@node3.emqx.io, emqx@node4.emqx.io"
    - EMQX_ZONE__EXTERNAL__RETRY_INTERVAL=2s
    - EMQX_MQTT__MAX_TOPIC_ALIAS=10
    volumes:
        - ./tmp/emqx.lic:/opt/emqx/etc/emqx.lic
    healthcheck:
      test: ["CMD", "/opt/emqx/bin/emqx_ctl", "status"]
      interval: 5s
      timeout: 25s
      retries: 5
    networks:
      emqx-bridge:
        aliases:
        - node1.emqx.io

  emqx2:
    image: emqx/emqx
    ports:
      - 18085:18083
      - 1885:1883
    environment:
    - EMQX_NAME=emqx
    - EMQX_HOST=node2.emqx.io
    - EMQX_CLUSTER__DISCOVERY=static
    - EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io, emqx@node3.emqx.io, emqx@node4.emqx.io"
    - EMQX_ZONE__EXTERNAL__RETRY_INTERVAL=2s
    - EMQX_MQTT__MAX_TOPIC_ALIAS=10
    volumes:
        - ./tmp/emqx.lic:/opt/emqx/etc/emqx.lic
    healthcheck:
      test: ["CMD", "/opt/emqx/bin/emqx_ctl", "status"]
      interval: 5s
      timeout: 25s
      retries: 5
    networks:
      emqx-bridge:
        aliases:
        - node2.emqx.io

  emqx3:
    image: emqx/emqx
    ports:
      - 18086:18083
      - 1886:1883
    environment:
    - EMQX_NAME=emqx
    - EMQX_HOST=node3.emqx.io
    - EMQX_CLUSTER__DISCOVERY=static
    - EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io, emqx@node3.emqx.io, emqx@node4.emqx.io"
    - EMQX_ZONE__EXTERNAL__RETRY_INTERVAL=2s
    - EMQX_MQTT__MAX_TOPIC_ALIAS=10
    volumes:
        - ./tmp/emqx.lic:/opt/emqx/etc/emqx.lic
    healthcheck:
      test: ["CMD", "/opt/emqx/bin/emqx_ctl", "status"]
      interval: 5s
      timeout: 25s
      retries: 5
    networks:
      emqx-bridge:
        aliases:
        - node3.emqx.io

  emqx4:
    image: emqx/emqx
    ports:
      - 18087:18083
      - 1887:1883
    environment:
    - EMQX_NAME=emqx
    - EMQX_HOST=node4.emqx.io
    - EMQX_CLUSTER__DISCOVERY=static
    - EMQX_CLUSTER__STATIC__SEEDS=emqx@node1.emqx.io, emqx@node2.emqx.io, emqx@node3.emqx.io, emqx@node4.emqx.io"
    - EMQX_ZONE__EXTERNAL__RETRY_INTERVAL=2s
    - EMQX_MQTT__MAX_TOPIC_ALIAS=10
    volumes:
        - ./tmp/emqx.lic:/opt/emqx/etc/emqx.lic
    healthcheck:
      test: ["CMD", "/opt/emqx/bin/emqx_ctl", "status"]
      interval: 5s
      timeout: 25s
      retries: 5
    networks:
      emqx-bridge:
        aliases:
        - node4.emqx.io

  client:
    image: python:3.7.2-alpine3.9
    depends_on:
      - emqx1
      - emqx2
      - emqx3
      - emqx4
    tty: true
    networks:
        emqx-bridge:

networks:
  emqx-bridge:
    driver: bridge

```
#### 启动docker-compose
进入docker-compose.yaml文件所在的目录,一键启动
```
$ docker-compose -p my_emqx up -d
```
#### 查看集群状态
```
$ docker exec -it [EMQ CONTAINER ID] sh -c "emqx_ctl cluster status"
###显示如下表示配置成功
Cluster status: #{running_nodes =>
                      ['emqx@node1.emqx.io','emqx@node2.emqx.io',
                       'emqx@node3.emqx.io','emqx@node4.emqx.io'],
                  stopped_nodes => []}
```

### 配置nginx反向代理
#### 配置emqx.conf文件
新建emqx.conf文件,配置如下:
```
stream
{
    upstream emqx_cluster {
	    zone tcp_servers 64k;
	    hash $remote_addr;
        server 172.16.0.90:1883 max_fails=2 fail_timeout=30s;
        server 172.16.0.90:1885 max_fails=2 fail_timeout=30s;
	    server 172.16.0.90:1886 max_fails=2 fail_timeout=30s;
	    server 172.16.0.90:1887 max_fails=2 fail_timeout=30s;
    }
 
    server{
        listen  1884;
        proxy_pass emqx_cluster;
	    proxy_buffer_size 4k;
   }
}

```
#### 把上述配置植入到nginx配置文件nginx.conf中
编辑nginx.conf,在pid行下面多加入一行include
```
include /etc/nginx/emqx.conf
```
保存退出
#### 重启nginx
```
$ service nginx restart
```