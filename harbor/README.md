# Harbor vmware开发的docker registry

> https://github.com/goharbor/harbor

## http 方式 

*harbor.cfg配置*

```
#DO NOT comment out this line, modify the value of "hostname" directly, or the installation will fail.
hostname = 172.30.81.193

#The protocol for accessing the UI and token/notification service, by default it is http.
#It can be set to https if ssl is enabled on nginx.
ui_url_protocol = http
```

**registry 172.30.81.193:80**


添加非https端口信任

*/etc/docker/daemon.json*

```
{"insecure-registries":["172.30.81.193:80"]}

systemctl restart docker
```

## https 方式

1. cfssl生成server.key,server.crt

2. 修改配置文件harbor.cfg

```
ui_url_protocol = https

customize_crt = on

#The path of cert and key files for nginx, they are applied only the protocol is set to https
ssl_cert = /data/cert/server.crt
ssl_cert_key = /data/cert/server.key

```

3. 客户端ca的信任

*ubuntu and centos*

```
echo -n | openssl s_client -showcerts -connect registry.wizlawgic.com:443 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /etc/ssl/certs/harbor.crt

systemctl restart docker
```

## 删除镜像

1. 管理界面删除

2. 停止harbor服务

`docker-compose stop`

3. 运行删除指令

`docker run -it --name gc --rm --volumes-from registry vmware/registry:2.6.2-photon garbage-collect --dry-run /etc/registry/config.yml`

4. 开启服务

`docker-compose start`
