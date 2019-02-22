# redis

> redis 是C语言写成的key-value数据库，与memcached类似，有丰富的数据类型

## redis与memcached区别和比较

1. redis不光支持简单的k/v类型的数据，还支持list，set，zset，hash

2. redis支持备份，支持master-slave方式的备份

3. redis支持持久化，rbd方式和aof方式

4. redis是单进程单线程



## 配置 rbd

注释掉，取消rbd
```
save 900 1
save 300 10
save 60 10000
```

## 配置 aof

```
appendonly yes
appendfsync everysec
```


## 配置主从

*从配置中加入*

```
slaveof master-ip master-port
```

## 哨兵模式

> 监控主和从，自动切换主从

1 sentinel(193) + 1 master(192) + 2 slave(193,194)

*sentinel配置*

```
daemonize yes
pidfile "/var/run/redis/redis-sentinel.pid"
logfile "/var/log/redis/redis-sentinel.log"
bind 0.0.0.0
port 26379
dir "/var/lib/redis"
sentinel monitor mymaster 172.30.81.192 6379 1
sentinel down-after-milliseconds mymaster 3000
sentinel failover-timeout mymaster 10000
sentinel parallel-syncs mymaster 2
sentinel config-epoch mymaster 204
maxclients 4064
sentinel leader-epoch mymaster 204
sentinel known-slave mymaster 172.30.81.194 6379
sentinel known-slave mymaster 172.30.81.193 6379
sentinel current-epoch 204
```

确保sentinel有写redis.conf和sentinel.conf的权限

主从切换后，sentinel会改写上述文件
