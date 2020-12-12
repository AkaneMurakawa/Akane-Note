---
title: linux从入门到放弃——07Shell程序设计
date: 2017-8-25 23:54:43
categories: Linux
comments: true
---
# linux从入门到放弃——07Shell程序设计



### 什么是shell

对于所有登录到unix系统的用户，系统都要为其开一个程序来管理用户与unix系统的交互工作，这个程序就是shell。

常见的有Bourne shell(B-shell)及Korn shell(K-shell)，C shell。我們用的話就是B-shell

shell作为一种用户与操作系统交互的工具与平台，shell的基本功能：

1. 命令的解释执行
2. 系统环境变量的设置
3. 输入/输出的重定向管理
4. shell程序的设置
5. 连通通道建立


#### Shell常见的语法规则：
在shell命令解釋時，可按照正則表達式識別文件名或者目錄名

```
cmd& :表示在后台执行该命令　　　常用
cmd1；cmd2 :表示在同一行中输入多个命令，shell将按顺序执行
(cmd1;cmd2) :表示创建一个子shell完成命令，这里酱cmd1和cmd2视为一个命令组加以执行　　
cmd1 | cmd2 :管道，表示用cmd1的输出作为cmd2的输入完成命令操作　　常用
cmd1 `cmd2`: 这里cmd2命令用一个（上/反引号）命令括起来，表示一种命令结果替换功能。
用cmd2的输出作为cmd1的输入完成命令操作。

cmd1 && cmd2 :逻辑与
cmd || cmd2 ：逻辑或

```

#### 标准流重定向与管道线控制：
像如果我們直接在終端將命令輸入'cd pwd'，這樣稱爲標準流重定向．
```
cmd > file　將輸出重定向到文件
cmd < file　將輸入重定向到文件
cmd >> file　將輸出追加方式重定向到文件

$ls -l > dir1
$pwd > dir1    用>则覆盖文件内容
date >> dir1   用>>不覆盖

prog < infile >outfile 執行效果：prog程序從infile中讀取參數，然後將結果送入到outfile中．順序執行

管道线控制
$ls -l >file
$wc -l >file
$rm file
等价于:
$ls -l | wc -l
```

#### 錯誤流重定向
```
首先我們可以通過命令查詢
hanaeyuuma@suzuki:~$ ls /dev/std* -l
lrwxrwxrwx 1 root root 15 Apr 16 22:22 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Apr 16 22:22 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Apr 16 22:22 /dev/stdout -> /proc/self/fd/1

cc fsfas > log　當我們如果輸錯了命令，錯誤信息流就會被默認重定向爲顯示器
cc fsfas 2>& log 將錯誤信息流重定向到log文件
cmd >file 2>&1 將輸出流和錯誤流同時重定向到log文件
注: &沒有固定意思，放在>後面表示file目標不是一個文件，而是一個文件描述符．

```

#### 運行shell
方法１：輸入輸出重定向　sh < hello.sh
方法２：作爲解釋器參數　sh hello.sh　大多數是用這種
方法３：作爲可執行程序　./hello.sh 首先要給可執行的權限


### shell程序编写格式
shell程序不需要进行编译，它是按行解释执行的，习惯上脚本文件的第一行总是以类似下面形式开头
```
#!/bin/sh
指明该shell程序使用系统中的B-shell解释器来解释执行
```

#### Shell的环境变量：
在主目錄下，運行命令'ls -l .bash*',可以看到一些關於bash的文件
```
.bash_history　
.bash_logout
.bash_profile
.bashrc　每次打開一個新的shell，需要執行的命令序列
.inputrc 用戶的鍵盤設定，以及針對用戶終端的鍵盤位置配置信息．
```

**shell的变量的定义：**
```
局部变量
UNIX="systemV" 變量名和等號之間不能有空格
$echo ${UNIX}  引用shell的变量:  ${变量名}

全局变量
UNIX=systemV
export $UNIX  将UNIX变量转换为shell的全局变量
$echo ${UNIX} 


env | grep myvar
set -a myvar 或者unset
declare myvar="dfa"
查看set/env/printenv

所以我們就可以使用變量做一些事情了，比如：/home/PS1
ps1=[\u@\h\w]\$
通過這樣的方式訪問路徑
```

**不同引号对shell变量产生的不同效果：**
1. 单引号'':shell解释程序将单引号中的内容看成纯粹的字符串信息。
```
$ file=report
$ echo 'The time is 'date',the file is $file'
结果：The time is 'date',the file is $file
```
2. 双引号"":shell解释程序将引号内的shell解释执行
```
$ file=report
$ echo "The time is 'date',the file is $file"
结果：The time is 'date',the file is report
```
3. 反引号``:表示变量中存放的是执行命令的结果
```
$ TT=`date`
$ echo $TT
结果：Mon Apr  9 20:38:24 DST 2018
```

到這裏先吧，下次說一下環境變量的配置，作爲一名開發者，環境變量配置是必不可少的．

