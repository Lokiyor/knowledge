# Ngine配置
##### 配置真是ip
server:location {
    proxy_set_header X-Real-IP $remote_addr;
}