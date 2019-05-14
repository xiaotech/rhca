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
ceph-deploy new node193 # 初始化集群信息，会在192上当前目录生成配置文件和日志，可以考虑添加如下参数

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


# osd的删除

```
ceph osd out osd.1 #权重设为0，pg迁移,ceph osd in osd.1迁入
systemctl stop ceph-osd@1.service # 停止服务
ceph osd crush remove osd.1 # crush 中删除
ceph osd rm 1 # 集群中删除
ceph auth del osd.1 #删除key，否则再次加入会导致key不匹配
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


# crush 设置，数据最终落在哪个osd，由crush确定

```
ceph osd getcrushmap -o crushmap.key #获取加密的crushmap
crushtool -d crushmap.key -o crushmap #解密crushmap
ceph osd  setcrushmap -i crushmap.key # 配置crushmap

命令行方式

ceph osd crush add-bucket name type #添加bucket
ceph osd crush move entry type=bucket-name #把entry移动到对应bucket下

ceph osd crush rule create-simple name root type # 添加rule，首次查找root，故障域type



手动管理crush
osd crush update on start = false

```

# pool ,数据存储的逻辑单元，所有的对象都存在pool中，pool和rule绑定决定数据在哪个osd

# 块存储的使用

```
rados mkpool rbd  #创建rbd pool

ceph osd pool application enable rbd rbd 设置rbd pool 应用为rbd

rbd create test1 --size 1024M # 创建1G的快存储

rbd map test1 # 映射块驱动，如出错ceph osd crush tunables hammer

rbd showmapped # 查看映射

```

# 文件系统使用,mds已安装

```
ceph osd pool create cephfs_data 32 32 # 创建数据存储池

ceph osd pool create cephfs_metadata 32 32 # 创建元数据存储池

ceph fs new cephfs cephfs_metadata cephfs_data # 创建cephfs


ceph-fuse -m 172.30.81.193:6789 /mntfs # 用户态挂载数据

mount -t ceph 172.30.81.193:/ /cephfs/ -o name=admin,secret=AQAdoNNcdaYaGhAACoWCmeoN5Nx82y03+yOLbg== # 内核态的挂载

```

# 对象存储的使用，rgw已安装

```
radosgw-admin user create --uid=xj-id --display-name=xiaojun --system # 创建对象存储的用户

radosgw-admin user info --uid=xj-id # 查询用户的key


dashboard要可用设置如下：

ceph dashboard set-rgw-api-access-key ***
ceph dashboard set-rgw-api-secret-key ***


s3cmd等使用配置如下
dns服务器泛域名解析,ceph.conf配置加入rgw dns name = ceph.x

key 填对,/root/.s3cfg
host_base = ceph.x:7480
host_bucket = %(bucket).ceph.x:7480
```


# libvirt 使用ceph

```

1. ceph端操作

创建存储池
ceph osd pool create libvirt-pool 128 128

创建libvirt授权用户
ceph auth get-or-create client.libvirt mon 'allow r' osd 'allow class-read object_prefix rbd_children, allow rwx pool=libvirt-pool'

2. libvirt端操作

拷贝ceph.conf和client admin key

创建快照测试
qemu-img create -f rbd rbd:libvirt-pool/new-libvirt-image 2G 和在ceph端创建rbd文件一样效果


libvirt配置认证
cat > secret.xml <<EOF
<secret ephemeral='no' private='no'>
        <usage type='ceph'>
                <name>client.libvirt secret</name>
        </usage>
</secret>
EOF

virsh secret-define --file secret.xml

ceph auth get-key client.libvirt | sudo tee client.libvirt.key
virsh secret-set-value --secret {uuid of secret} --base64 $(cat client.libvirt.key) && rm client.libvirt.key secret.xml

vm xml配置

...
<disk type='network' device='disk'>
        <source protocol='rbd' name='libvirt-pool/new-libvirt-image'>
                    <host name='{monitor-host}' port='6789'/>
        </source>
        <auth username='libvirt'>
          <secret type='ceph' uuid='9ec59067-fdbc-a6c0-03ff-df165c0587b8'/>
        </auth>
        <target dev='vda' bus='virtio'/>
</disk>


3. openstack与ceph集成，主要配置glance，nova，cinder 加入 user，pool，secret文件

```

# ceph 状态

1. active: ceph互联完成互联

2. clean： ceph互联成功，没有偏离的pg,副本数达到指定数量

3. degrade： 主osd接收数据写入，副本osd在没有完成写入向主osd报告前

4. recovering： 某osd挂了，pg状态落后其他osd，启动后数据处于恢复状态

5. backfill: 新osd加入，crush将现有集群的部分pg分配给他，处于回填中

6. unclean: 副本时没有达到指定的数

7. inactive:  不能提供读写服务，等待最新的osd回到up状态

8. stale： osd有阵子没有想监视器报告状态了

9. remapping: osd添加，删除pg重新映射 
