# awk 使用说明

## awk工作模式
awk适合文本处理和报表生成，语法类似C语言;

读取输入文件，处理数据，输入数据

## 使用方法

1. awk 内置变量

| 变量名|属性|
|-|-|
|$0|当前读入行|
|$1~$n|当前记录第n个字段|
|FS|分割符号，默认空格|
|RS|分行符号，默认换行|
|NF|当前记录的字段个数|
|NR|当前记录的行数|
|OFS|输出字段的分割符|
|ORS|输出字段的分行符|

```
分割符空格或逗号
awk -F'[ ,]+' ...
```

2. 使用语法

### 常见使用
> awk '{action}' {filename}

* 打印passwd第一列

`awk -F: '{print $1}' /etc/passwd`

* 打印第一列和第三列的信息

`awk -F: '{print $1,$3}' /etc/passwd`

### 正则
> awk '/pattern/{action}' {filename}

* 打印root行

`awk -F: '/root/{print $0}' /etc/passwd`

或

`awk -F: '$1~/root/{print $0}' /etc/passwd`

### 判断,使用if一定要在{}中
> awk '{if(exp){action}}' {filename}

`awk -F: '$1=="root"{print $0}' /etc/passwd`

或

`awk -F: '{if($1=="root"){print $0}}' /etc/passwd`

3. 循环

> * awk '{while(exp){action}}' {filename}
> * awk '{for(init;exp;action){action}}' {filename}

```
awk 'BEGIN{while(1){x++;print x;if (x==10)break}}'
awk 'BEGIN{for(i=1;i<=10;i++){print i}}'
```

4. 数组

`awk 'BEGIN{ a[1]=1;a["two"]=2;for(i in a){print i,a[i]}}'`


5. 字符串操作

* 字符串替换gsub(src,dst,string)

`awk 'BEGIN{info="this is test";gsub("is","IS",info);print info}'`

* 字符串查找index(string,str),下标从1开始，未找到返回0

`awk 'BEGIN{info="this is test";print index(info,"is")}'`

* 字符串正则查处match(string,pattern)

`awk 'BEGIN{info="this is test";print match(info,"is")}'`

* 字符串截取substr(string,start_pos,len)

`awk 'BEGIN{info="this is test";print substr(info,6,2)}'`

* 字符串转数组split(string,A_name,"split")

`awk 'BEGIN{info="this is test";split(info,A," ");for(i in A){print i,A[i]}}'`

* 获取字符串的长度

`awk 'BEGIN{info="this is test";print length(info)}'`

* 格式化输出printf

`awk 'BEGIN{printf"%s-%s\n","hello","world"}'`

# 高级应用

查看git的历史提交，并统计每天的提交数

`
git log --stat|grep -e "Date: "  -e " changed"|awk -F'[:,]+' '{date[NR]=substr($2,0,length($2)-3);if($0 ~ / changed/){gsub(/[^0-9]+/,"",$2);gsub(/[^0-9]+/,"",$3);add[date[NR-1]]+=$2;del[date[NR-1]]+=$3}}END{for(i in add){print i","add[i]","del[i]}}'
`
