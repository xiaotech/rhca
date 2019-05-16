# fluentd

1. ubuntu16 apt源

```
curl https://packages.treasuredata.com/GPG-KEY-td-agent | apt-key add -
echo "deb http://packages.treasuredata.com/2/ubuntu/xenial/ xenial contrib" > /etc/apt/sources.list.d/treasure-data.list

systemctl start td-agent
```

2. 配置文件/etc/td-agent/td-agent.conf

es 输出的配置

```
<match app.*>
@type elasticsearch
host  es主机
port  es端口
index_name    fluentd.${tag}
type_name     my_type
flush_interval 2s #及时刷新缓冲区
include_timestamp true
</match>

``` 


syslog收集

```
<source>
  @type tail
  path /var/log/syslog.ok
  pos_file /tmp/syslog.ok.pos
  tag syslog
  refresh_interval 2s
  read_from_head true
  format syslog  #可以使用none，apache等
</source>
```

