# ubuntu 包环境配置

```
 wget -q -O- 'http://mirrors.163.com/ceph/keys/release.asc' | apt-key add -
 
 echo "deb http://mirrors.163.com/ceph/debian-mimic/ $(lsb_release -sc) main"| tee /etc/apt/sources.list.d/ceph.list
```


# 安装 环境

```

node192 :  ceph-deploy 机器

node193 ： mon ，mgr ，osd

node194 ：osd 
```

# 安装ceph包

```
 node192 : apt install ceph-deploy

 node193 : apt install ceph ceph-mon ceph-osd ceph-mds #也可以通过ceph-deploy install --no-adjust-repos node193安装

 node194 : apt install ceph ceph-mon ceph-osd ceph-mds #也可以通过ceph-deploy install --no-adjust-repos node194安装
```

# node193 作为mon步骤

*node192执行*

```
ceph-deploy new node193 # 会在192上当前目录生成配置文件和日志，可以考虑添加如下参数

osd pool default size = 2
public network = 172.30.81.0/24
rbd_default_features = 1
rgw dns name = ceph.x

ceph-deploy monitor create-initial # 在 node193 上执行mon安装指令

ceph-deploy admin node193  # 拷贝key文件到node193上

```
# node193,node194 osd的安装

```

ceph-deploy disk zap node193 /dev/vdb
ceph-deploy osd create --data /dev/vdb node193

ceph-deploy disk zap node194 /dev/vdb
ceph-deploy osd create --data /dev/vdb node194

```

# mgr 的安装

```
ceph mgr create node193

启动dashboard

ceph dashboard create-self-signed-cert # 自签名并应用

生成证书nginx反向代理可用，也可不用
mkdir dashboard-certs/ && cd  dashboard-certs
openssl req -new -nodes -x509 -subj "/O=IT/CN=ceph-mgr-dashboard" -days 3650 -keyout dashboard.key -out dashboard.crt -extensions v3_ca

ceph mgr module enable dashboard #启动

ceph mgr services  # 查看endpoint，注意ceph config set mgr mgr/dashboard/server_addr 0.0.0.0 高可用使用

dashboard set-login-credentials admin admin # 设置用户名密码
```
