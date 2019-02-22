# etcd

> ETCD拥有共享配置和服务发现，一致性的KV存储系统

## 1. 一致性方面使用raft算法

  节点有Leader，Follower，Candidate 三个角色之一

- 集群初始化时，每个节点都是Follower角色

- 集群中只有唯一一个Leader，通过心跳与其他节点通信

- 当Follwer一定时间没有收到Leader心跳，会变成Candidate，发起选主;

- Candidate收到Leader心跳后，立即终止选主，变成Follwer

## 2.日志复制

  Leader会将每次操作形成日志并持久化到本地，然后发送给其他节点，当有一半以上节点

成功返回，那么认为该日志是可以提交的。

## 3.实战

### 单节点

*/etc/etcd/etcd.conf 配置*

```
name: node193
data-dir: /var/lib/etcd
listen-peer-urls: http://172.30.81.193:2380
listen-client-urls: http://172.30.81.193:2379,http://127.0.0.1:2379
initial-advertise-peer-urls: http://172.30.81.193:2380
advertise-client-urls: http://172.30.81.193:2379
initial-cluster-state: new
initial-cluster-token: xiaotech
initial-cluster: node193=http://172.30.81.193:2380
```

**启动**
`etcd --config-file /etc/etcd/etcd.conf`

### 添加节点

```
1. etcd member add node194 http://172.30.81.194:2380

2. *node194 etcd配置*

name: node194
data-dir: /var/lib/etcd
listen-peer-urls: http://172.30.81.194:2380
listen-client-urls: http://172.30.81.194:2379,http://127.0.0.1:2379
initial-advertise-peer-urls: http://172.30.81.194:2380
advertise-client-urls: http://172.30.81.194:2379
initial-cluster-state: existing
initial-cluster-token: xiaotech
initial-cluster: node193=http://172.30.81.193:2380,node194=http://172.30.81.194:2380

etcd --config-file /etc/etcd/etcd.conf

```

### 删除节点

```
etcdctl member list
etcdctl member remove id
```

### 备份恢复

备份 data-dir目录

### 常用操作

查看集群节点

`etcdctl member list`

查看集群健康

`etcdctl cluster-health`


### ubuntu添加systemd启动

编辑/lib/systemd/system/etcd.service

```
[Unit]
Description=Etcd Server
Documentation=https://github.com/coreos/etcd
After=network.target

[Service]
User=root
Type=notify
EnvironmentFile=-/etc/etcd/etcd.conf
ExecStart=/usr/bin/etcd --config-file /etc/etcd/etcd.conf
Restart=on-failure
RestartSec=10s
LimitNOFILE=40000

[Install]
WantedBy=multi-user.target
```

`systemctl daemon-reload`

`systemctl enable etcd`

`systemctl start etcd`

 
