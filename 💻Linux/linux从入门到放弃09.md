---
title: linux从入门到放弃——09Shell编程
date: 2017-8-25 23:55:44
categories: Linux
comments: true
---
# linux从入门到放弃——09Shell编程

### 一、B-shell常用预定义变量及环境变量定义表

```
USER    用户名
HOME    用户注册目录
PATH    命令访问路径
CDPATH  Cd命令路径
PS1     系统提示符
PS2     辅助提示符
TERM    终端类型
SHELL   内定运行的shell
```
### 二、shell中常用特殊变量的定义


```
$#  位置参数的个数（数字键：43)
$?  前一命令返回的状态值(0为正常)【常用】（数字键：4？)
$$  当前shell进程的pid值（数字键：44)
$!  最近访问的后台进程的pid值（数字键：41)
$*  用单字符串显示传递参数（数字键：48)
```

![](images/shell.png)

### 三、变量替换
```
${Var:-word} 
${Var:=word}
${Var:+word}
用命令做变量替换
now = `date`
echo $now
```
### test命令
test命令返回真值为0,否则返回假值非0.

1.test对文件特性的测试

```
test -[dfrwxs] file
-d file 文件file存在且为目录
-f 文件存在且为普通目录
-r 文件存在且可读
-w 文件存在且可写
-x 文件存在且可执行
-s 文件存在且长度非0
例如:
test -d /home/HanaeYuuma/test
```
2.test对字符串内容的测试
```
test s 当前字符串s非空,为真
test-zs 当前字符串s为空,为真
test s1 = s2 当字符串相等为真
test s1 != s2 不等时为真
注意: 后面两个语句中在= 和!= 两边要加上空格,否则可能出现提示错误.
```
3.test对整数n的测试
```
test n1 -eq n2  相等为真
同理: 
-ne (not equal)
-lt (less than)
-le (less than or equal)
-gt (greate than)
-ge (greate than or equal)
```
### 四、条件控制语句
#### if
```
1.if-then-fi
if  [condition]
then
    commands
fi

2.if-then-else-fi
if  [condition]
then
    commands
else
    commands
fi

3.if-then-elif-then-fi
if  [condition]
then
    commands
elif  [condition]
then
    commands
else
    commands
fi
```
#### for
```
for-in-do-done
for variable    in  list-of-values
do
    commands
done
```

#### while
```
while-do-done
while[condition]
do
    commands
done
```

#### until
```
until-do-done
until[condition]
do
    commands
done
```

### 五、shell程序的调试方法
```
sh -[vx] shell.sh
-v 完成详细跟踪,执行时首先逐行读入命令,在标准输出上显示该命令执行的实际内容.
然后执行语句,直到有语法错误停止执行

-x实际命令运行的跟踪.在命令执行前,首先显示经过变量替换后的命令行内容,然后再执行.
```
