# sed使用说明

> sed作为流编辑器，读取操作的数据，缓存在缓存区里，数据操作，处理


## 常见使用方法

1. 匹配行之后添加数据

`sed '/reg/a str' filename`

`sed '1a str' filename`

2. 匹配行之前添加数据

`sed '/reg/i str' filename`

`sed '1i str' filename`

3. 匹配行修好数据

`sed '/reg/c str' filename`

`sed '1c str' filename`

4. 删除数据

`sed '/reg/d' filename`

`sed '1d' filename`

`sed '1,/reg/d' filename`

5. 查询数据

`sed  -n '/reg/p' filename`

`sed -n '1p' filename

`sed -n '1,/reg/p' filename`

6. 替换数据

`sed 's/reg/str/' filename`

`sed 's/reg/**&**/' filename`

`sed 's/\(reg\)/**\1**/' filename`

6.多个操作语句

`sed -e '/reg/d' -e '/reg1/d' filename`

