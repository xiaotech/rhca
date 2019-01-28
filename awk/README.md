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
> awk 'action' {filename}

* 打印passwd第一列
`awk -F: '{print $1}' /etc/passwd

* 打印第一列和第三列的信息
`awk -F: '{print $1,$3}' /etc/passwd`

### 正则
> awk '/pattern/{action}' {filename}

* 打印root行
awk -F: '/root/{print $0}' /etc/passwd

或

awk -F: '$1~/root/{print $0}' /etc/passwd

### 判断,使用if一定要在{}中
> awk '{if(exp){action}}' {filename}

awk -F: '$1=="root"{print $0}' /etc/passwd

或

awk -F: '{if($1=="root"){print $0}}' /etc/passwd

