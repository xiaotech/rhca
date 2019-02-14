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



