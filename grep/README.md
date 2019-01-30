# grep

> grep [option] (pattern1|pattern2) file1 file2 ...

1. 搜索匹配文件

`grep text text*`

2. 递归搜索目录

`grep text -r ./*`

3. 显示搜索到的文件名

`grep -l text text*`

4. 显示不搜索到的文件名

`grep -L text text*`

5. 显示匹配行及以下n行

`grep -A 3 text text*`

6. 显示匹配行及以上n行

`grep -B 3 text text*`

7. 显示匹配行上下各n行

`grep -C 3 text text*`

8. 显示匹配的行数

`grep -c text text*`

9. 显示不包含的行

`grep -v text text*`

10. 搜索不区分大小写

`grep -i text text*`

11. 正则匹配

`grep -E pattern text*`
