# Nacos集群监控
### 前置环境搭建
1. [nacos集群搭建](https://github.com/Lokiyor/knowledge/blob/master/Docker/Docker%E9%95%9C%E5%83%8F%E5%AE%B9%E5%99%A8%E5%AE%89%E8%A3%85/nacos/Docker%E6%90%AD%E5%BB%BANacos%E9%9B%86%E7%BE%A4.md)
2. [prometheus安装](https://github.com/Lokiyor/knowledge/blob/master/Docker/Docker%E9%95%9C%E5%83%8F%E5%AE%B9%E5%99%A8%E5%AE%89%E8%A3%85/prometheus/Docker%E5%AE%89%E8%A3%85prometheus.md)
3. [grafana安装](https://github.com/Lokiyor/knowledge/blob/master/Docker/Docker%E9%95%9C%E5%83%8F%E5%AE%B9%E5%99%A8%E5%AE%89%E8%A3%85/grafana/Docker%E5%AE%89%E8%A3%85grafana.md)

### 相关配置
#### 修改 prometheus.yml 的配置
1. 在scrape_configs 属性下多配置一个job_name，相关配置如下
```
scrape_configs:
  # 添加如下配置
  - job_name: nacos
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    # 信息收集频率
    scrape_interval: 5s
    # prometheus信息路径
    metrics_path: '/nacos/actuator/prometheus'
    static_configs:
      # nacos集群地址端口，内网通配内网，不同配外网
      - targets: ['xxx.xxx.xxx.xxx:8848','xxx.xxx.xxx.xxx:8848']

```
2. 保存后重启prometheus

#### 配置grafana

##### grafana配置仪表模板
1. 进入grafana控制台，点击Configuration-Add data source
2. Name 配置为Prometheus (有些版本为小写的p(prometheus)，不同版本不同，按情况来，哪个行配哪个)
3. url配置为prometheus地址: http://xxx.xxx.xxx.xxx:9090
4. 其他默认即可
5. Save & Test 保存配置
##### 配置nacos监控仪表盘模板
1. 下载nacos仪表盘模板
- nacos普通仪表盘(一般用这一项)
  - [nacos-grafana.json](https://github.com/nacos-group/nacos-template/blob/master/nacos-grafana.json)
- nacos异步仪表盘
  - [nacos-sync-grafana.json](https://github.com/nacos-group/nacos-template/blob/master/nacos-sync-grafana)
2. 进去grafana, Create-Import
3. 点击 Upload .json file ,选中刚刚下载的仪表板模板
4. 命名Name, Folder选择-默认General, Unique identifier (uid) - change 重新生成(若有重复自行修改下不重复即可)
5. Import 导入仪表板模板

#### 验证查看
进入Dashboards，选择刚刚配置的仪表盘name，进入nacos监控
> 此处应有坑，哈哈哈哈哈  
> 突然发现service count一项显示的是N/A，进去查看详情，发现监控的配置写法是max(nacos_monitor{name='domCount'})。此时想到，这个数据来源于prometheus收集的nacos集群数据，进入http://xxx.xxx.xxx.xxx:8848/nacos/actuator/prometheus 搜索domCount，发现并没有这一项。但是却又 serviceCount 这一项，然后就把service count的监控配置改成(nacos_monitor{name='serviceCount'})，就顺利监控到服务数量了。不知到时nacos版本问题，还是github官方给的配置有问题，哈哈。
> 