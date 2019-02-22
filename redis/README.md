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


