# Docker安装prometheus
#### 拉去最新镜像
```
$ docker pull prom/prometheus
```
#### 创建配置文件
```
$ mkdir -p /data/docker/prometheus/conf
$ touch prometheus.yml

# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets:
      # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']

```
#### 启动容器
```
$ docker run -d --name nacos-prometheus --net=host -p 9090:9090 -v /data/docker/prometheus/conf/prometheus.yml:/etc/prometheus/prometheus.yml  prom/prometheus
```
> 此处本有坑, 不配置 --net=host 的时候， -p 9090:9090 是有效的， 但是内外网都访问不到9090端口，所以就算启动成功，也没用
> 加上此配置，-p 9090:9090 是无效的，所以prometheus默认的9090端口占用了宿主机的9090端口，此时是可以访问的