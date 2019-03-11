# linux常用命令操作

1. cut 

显示passwd 第一列，第三列

`cut -d: -f1,3 /etc/passwd`

显示passwd 第一列到第三列

`cut -d: -f1-3 /etc/passwd`

2. tr 字符串处理函数

删除出现过的字符

`echo helloworld|tr -d [root]`

压缩连续重复出现的字符

`echo helloworld|tr -s [root]`

替换字符

`echo helloworld|tr [root] [ROOT]`

3. diff 比较文件和文件夹的不一样

* 文件比较

`diff file1 file2`

`diff -y file1 file2`

* 文件夹比较

`diff -r dir1 dir2`

* 只列出不一样的文件

`diff -rq dir1 dir2`


4. find 搜索文件

`find / -name *.conf -mtime -1 -perm 450 -size 1M`


5. pwd显示当前所在路径

* 显示真实的物理路径

`pwd -P`

* 显示逻辑路径

`pwd -L`

6. wc 统计次数

`wc -l`

`wc -w`

`wc -c`


7. sort 排序

-t 分割符
-k 关键字
-nr 反向排序

8. uniq

-c 统计次数
-i 忽略大小写

9. ps 查看进程信息

`ps aux`

`ps -elf`

`ps -eo pid,cmd,%cpu,%mem`

10. /etc/skel用户目录初始文件

`/etc/skel下面的文件会添加到新建的用户目录下面`

11. ldconfig

* 往/lib，/usr/lib下添加so文件，无需修改ld.so.conf,只需要执行ldconfig

* 往其他地方添加so文件，需要修改ld.so.conf,然后执行ldconfig

* 不能通过ldconfig修改的，修改LD_LIBRARY_PATH修改

12. jq json的数据处理工具

获取对应的数据

`jq '.key.array[].key'`

带选择的获取数据

`jq '.key.array[]|select(.key.key=="str")|.key'`

13. split 分割文件

`split -b sizeK file prefix_`
