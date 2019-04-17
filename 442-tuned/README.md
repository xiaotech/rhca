# 系统监控

* mpstat

* iostat

* iotop

* vmstat

* sar

sar可以保存历史数据，后续分析

sar 1 1 -o save.data

cpu: -q -u

mem: -r

disk: -d

network: -n DEV

* gnuplot 绘图

# 磁盘调优

* cache ( buffer cache, page cace)

* io schedule

1. noop: 电梯调度算法，io调度器不做过度的调度，适用于虚拟机，ssd盘

2. daedline: 可预期的最长等待时间，适用于宿主机，多线程，数据库等场景

3. cfq: 完全公平调度算法，有三个优先级，最复杂的调度算法，默认的调度算法

4. anticipatory: 读之后有延迟的算法，适用于大文件顺序读场景
