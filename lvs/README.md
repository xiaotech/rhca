# LVS - linux virtual server

1. ## DR 模式

  DR模式，客户端请求包到达负载均衡虚拟的IP端口后，负载均衡改写请求包的目的MAC地址为后端RS的MAC地址;

真实服务器收到请求后直接返回给客户端，不经过负载均衡，DR模式效率最高，适合下行流量大的业务场景。

1. **数据包到达负载均衡后，只有源，目的MAC地址被改写，IP ，端口不变**

2. **RS必须还回口绑定VIP且端口必须和负载均衡一致，才能收包处理**

3. **RS收包处理后直接返回客户端，负载均衡和RS必须位于同一个子网**

*配置步骤*

* LB上

```
ifconfig eth0:0 172.30.81.213/24
ipvsadm -A -t 172.30.81.213:80 -s rr
ipvsadm -a -t 172.30.81.213:80 -r 172.30.81.194 -g
ipvsadm -a -t 172.30.81.213:80 -r 172.30.81.194 -g
```

* RS上

```
ifconfig lo:0 172.30.81.213/32
```


2. ## NAT模式

  NAT模式数据包请求和返回都经过LB，LB对请求的数据包做NAT;
对返回的数据包做SNAT

1. **LB会修改数据包的地址**

2. **RS的网关必须设置成LB的地址**

3. **LB和RS必须位于相同的子网,客户端和RS必须位于不同的子网**

*配置步骤*

LB配置
```
ipvsadm -A -t 172.30.81.192:80 -s rr
ipvsadm -a -t 172.30.81.192:80 -r 192.168.11.2 -m
ipvsadm -a -t 172.30.81.192:80 -r 192.168.11.3 -m
```

RS配置
```
route add default gw 192.168.11.1
```

3. ## FULLNAT模式
  
  FULLNAT会对数据包的请求和返回都做SNAT和DNAT，对组网没有要求

4. ## 负载均衡模式

1. rr  轮流调度

2. wrr 会根据rs配置的权重按比例分发

3. lc 最小连接数

4. wlc 加全最小连接数

5. dh 根据目的IP做hash

6. sh 根据源IP做hash 


5. ## 操作命令

* -A 添加服务

* -s 设置调度算法

* -a 添加rs

* -r real server

* -g DR模式

* -m NAT模式

* -i tunnul模式

* -b fullnat模式

* -d 删除rs

* -C 删除所有服务

* ipvsadm -Sn > lvs.save 保存配置

* ipvsadm -R < lvs.save 恢复配置

