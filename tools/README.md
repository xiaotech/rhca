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
