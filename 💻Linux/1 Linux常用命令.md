# Linux常用命令

## Linux的体系结构

分用户态和内核态

![image-20201129114311825](images\image-20201129114311825.png)



在这里对Linux常用命令整理，只列举常用的，因为命令和可选参数实在太多了。分几个方面：

1. 查询日志
2. 查看服务信息（负载、内存）
3. 问题排查

手册：man 命令、https://wangchujiang.com/linux-command/



一般来说我们的命令都是这样的形式：

```
command [options] [arguments] 
说明：options选项，arguments参数
```



## 准备

第一步就是连接跳板机，可以用工具，例如：Xshell，也可以直接在web上去连接。

## 常用的简单命令

### **简单命令**

示例：

```
pwd：print working directory 查看自己所在的当前路径，当前目录

whereis：当然你也可以通过这个命令查找文件所在位置

clear：清除

history：查看命令历史，学习他人用到的命令
```



### **切换目录**

https://wangchujiang.com/linux-command/c/cd.html

- cd：change directory 接着进入到对应的目录下，在这里可以使用**绝对路径 | 相对路径**
- 选项
  - **.：当前目录**
  - **..：上级目录**
  - **~：用户主目录**
  - **\- ：返回此目录之前的所在的目录**
  - **/ ：根目录**

示例：

```
cd /
cd ./logs/
cd ..
cd -
cd ~
```



### **当前目录展示**

https://wangchujiang.com/linux-command/c/ls.html

- ls：list directory content 如果我们不知道当前目录下有什么，我们就可以用这个命令列出

- **选项：**
  - **-a ：隐藏的文件也会列出来**
  - **-l ：所有输出信息用单列的格式输出，而不是多列，一般我们用这个就看看文件的修改时间、大小**
  - **-t ：最近修改的文件显示在最上面，文件比较多的时候好用。**

示例：

```
ls -a
ll  注：等价于ls -l
ls -t

```





## Vim

linux上的编辑器，简单了解即可。

要想使用vi，首先就需要了解其工作模式：命令模式、编辑模式、末行模式

![image.png](images/vim.png)



基本上了解上面的一些就ok，另外就是如何编辑和退出。

当一开始进入的时候是**命令模式**，接着我们可以按下键盘上的**iao**任意一个，就会进入到编辑模式了。

编辑完后，我们可以看**ESC**回到命令模式，最后输入**：**进入到末行模式，也就是上图的**底线命令模式**，最后输入**wq!**保存退出即可。



### **常用操作**

注意：是末行模式、底线命令模式

- **:! 强制**
- **:w 保存**
- **:q 退出**
- **:q!强制退出**
- **:wq!**
- **:/搜索**
- set su显示行号



## 日志查询相关命令

上面的简单了解即可，接下来才是查询日志。在linux终端模式下并没有界面打开，因此我们第一个问题就是：如果查看文件内容？

上诉所讲的vi也是可以的，但是对我们来说使用太不友好了。

在学习之前，我们可以先想一下有什么需求：首先我们希望可以去搜索关键字或正则表达式，其次最好颜色也像上面一样可以标记颜色，最后可以看看查询内容的上下文。

Ok，下面你的问题都将会得到解答。



### 如何查询日志

如果你不知道日志存储的位置，在Java项目中，都会配置自己的日志存储路径。如果是用log4j2的话，对应的配置文件(log4j2.xml)，找到日志存储的位置。



#### 我的经验 - 查询日志步骤

1. 先定位是哪台服务器上的服务出了问题，连接上对应服务器，找到对应的日志存储位置

2. log4j2.xml日志文件达到一定大小会进行压缩，可能需要到压缩文件下查看，用zgrep命令。【不然你可能一直很奇怪为什么查不到信息】

3. **接着通过关键的信息去查找，比如：业务单号，业务数据等。找到对应的相关信息之后，可以通过对应信息所在的线程id去查找更多信息。**

   日志的打印格式(**Pattern**)可以在log4j2.xml配置文件下查询

```
2019-08-31 00:00:00.414 ERROR [SimpleAsyncTaskExecutor-1] 1e1111c8-1bf4-4e2c-af43-db0bda2ba85c 后面的没有列出来

日期——线程名——线程ID
<pattern>%d [${APP_NAME}] [%thread] %-5level %logger[%M] - %msg%n</pattern>
```



- **坑：在这里要看是异步线程，还是同步线程，还是多线程。不然你可能通过该线程id找不到对应的日志错误信息。如果没有线程id，那就只能通过线程名称了**
- **建议：如果是mq的消息，可以先找到mq消费的messageId，接着通过该messageId找到更多的信息。**

- **坑：当遇到一个错误信息的时候，你可以看看代码，然后通过版本控制器看看是不是最近有人改了代码，然后找出来暴打问一顿。（笑）**

- **补充：有些公司会将请求的方法打印出来，还可以根据方法的名称去搜索error.log信息**



### grep / zgrep / egrep

https://wangchujiang.com/linux-command/c/grep.html

grep：**G**lobal search **R**egular **E**xpression(RE) and **P**rint out the line

介绍：一个全面搜索表达式并把行打印出来的命令



命令：grep / zgrep / egrep

- grep：基本搜索 【常用】
- zgrep：压缩文件搜索，例如xxx-2020-11-23-1.log.gz【常用】
- egrep：正则表示式搜索， 等价于 grep -E 【常用】

示例：

```
grep "test" *.log
zgrep "test" xxx-2020-11-23-1.log.gz
egrep 'ing:[0-9]{4,}' *.log --color
```



#### **颜色标记** --color

-    --color=auto ：标记匹配颜色（能快速找到匹配的内容，高亮)【常用】

说明：不只是grep命令可以搭配，很多参数都可以，例如：tail、head、cat等

示例：

```
grep "test" *.log --color=auto
```



#### **ABC神器** -A -B -C

- -A 除了显示符合范本样式的那一行之外，并显示该行之后的内容【常用】
- -B 除了显示符合范本样式的那一行之外，并显示该行之前的内容【常用】
- -C 除了显示符合范本样式的那一行之外，并显示该行前后后的内容【常用】

- A：After后、B：Before前、C：content前后

示例：有时候我们不仅是想看到符合的内容，**还想看到其上下文，这时候就得借助ABC选项了。**

```
grep "aes-256-gcm" shadowsocks.log -B2
grep "aes-256-gcm" shadowsocks.log -C2
grep "aes-256-gcm" shadowsocks.log -A2
```



#### **正则查询** -E

- -E ：（**R**egular **E**xpression）使用正则表达式【常用】

示例：查询超时

```
cat /usr/local/logs/requestTime.log | grep -E "ing:[0-9]{4,}" -C 5
注：grep -E 等价于 egrep， 例如：egrep 'ing:[0-9]{4,}' *.log --color
```

 

#### **反转查找，致敬韦神** -v

- -v：（invert-match）输出除之外的所有行 **-v** 选项

示例：查询除了grep以外的行

```
grep -v "grep\|bash" 
```



### tail

介绍：一个查询文本命令，tail即尾部的意思，从尾部查找



#### **查看启动日志**

- -f：（follow）显示文件最新追加的内容【常用】

示例：在启动项目的时候，日志是一点点打印，通过这个参数-f就能一直查看追加的内容

````
tailf other.log 注：等价于tail -f other.log
````



#### **查看日志后500行**

- -n （--line-number）输出文件的尾部N（N位数字）行内容 【常用】
- 其它
  - tail file ：(默认显示文件file的最后10行)
  - tail +20 file ：(显示文件file的内容，从第20行至文件末尾)
  - tail -c 10 file ：（显示文件file的最后10个字符）

示例：当报错误时，可以马上到服务器上查看日志，直接查看后面多少行内容基本上就是对应的报错信息了

```
tail -n500 info.log
```



### head

介绍：一个查询文本命令，head即头部的意思，从头部查找。与tail相对



#### **查看日志前500行**

- -n 输出文件的尾部N（N位数字）行内容 【常用】

示例：

```
head -n500 info.log
```



### cat/zcat

介绍：连接多个文件并打印到标准输出



### 管道

介绍：可将多个命令连接起来，前一个指令的输出作为后一个指令的输入

示例： 

```
grep "test" *.log | more
```



### more

https://wangchujiang.com/linux-command/c/more.html

介绍：按页显示文本内容

为了控制滚屏，可以按Ctrl+S键，停止滚屏；按Ctrl+Q键可以恢复滚屏。按Ctrl+C（中断）键可以终止该命令的执行，并且返回Shell提示符状态。



交互操作：

- h（获得帮助信息）
- **Enter（向下翻滚一行）**
- **空格（向下滚动一屏）**
- q（退出命令）



### awk【特殊】

https://wangchujiang.com/linux-command/c/awk.html

介绍：awk是一种编程语言，一个强大的文本分析工具，，用于在linux/unix下对文本和数据进行处理。

 awk：命名取自三位创始人 Alfred Aho，Peter Weinberger, 和 Brian Kernighan 的 Family Name 的首字符



**常用命令选项**

- **-F fs**：fs指定输入分隔符，fs可以是字符串或正则表达式，如-F:
- **-v var=value**：赋值一个用户定义变量，将外部变量传递给awk
- **-f scripfile**：从脚本文件中读取awk命令
- **-m[fr] val**：对val值设置内在限制，-mf选项限制分配给val的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。

```bash
[root@test:/root] awk
用法: awk [POSIX 或 GNU 风格选项] -f 脚本文件 [--] 文件 ...
用法: awk [POSIX 或 GNU 风格选项] [--] '程序' 文件 ...
POSIX 选项:                     GNU 长选项:
        -f 脚本文件             --file=脚本文件
        -F fs                   --field-separator=fs
        -v var=val              --assign=var=val
        -m[fr] val
        -O                      --optimize
        -W compat               --compat
        -W copyleft             --copyleft
        -W copyright            --copyright
        -W dump-variables[=file]        --dump-variables[=file]
        -W exec=file            --exec=file
        -W gen-po               --gen-po
        -W help                 --help
        -W lint[=fatal]         --lint[=fatal]
        -W lint-old             --lint-old
        -W non-decimal-data     --non-decimal-data
        -W profile[=file]       --profile[=file]
        -W posix                --posix
        -W re-interval          --re-interval
        -W source=program-text  --source=program-text
        -W traditional          --traditional
        -W usage                --usage
        -W use-lc-numeric       --use-lc-numeric
        -W version              --version

提交错误报告请参考“gawk.info”中的“Bugs”页，它位于打印版本中的“Reporting
Problems and Bugs”一节

翻译错误请发信至 translation-team-zh-cn@lists.sourceforge.net

gawk 是一个模式扫描及处理语言。缺省情况下它从标准输入读入并写至标准输出。

范例:
        gawk '{ sum += $1 }; END { print sum }' file
        gawk -F: '{ print $1 }' /etc/passwd
```



#### **语法结构**

```
awk 'BEGIN{ print "start" } pattern{ commands } END{ print "end" }' file
```

- **BEGIN语句块**：在awk开始从输入流中读取行 **之前** 被执行【可选】
- **END语句块**：在awk从输入流中读取完所有的行 **之后** 即被执行【可选】
- **pattern语句块**：awk读取的每一行都会执行该语句块【可选】

示例：

```
seq 5 | awk 'BEGIN{ sum=0; print "总和：" } { print $1"+"; sum+=$1 } END{ print "等于"; print sum }' 
总和：
1+
2+
3+
4+
5+
等于
15
```



#### **语法示例**

```
# 默认的字段定界符是空格
echo -e "1 2 3" | awk '{print $1,$2,$3}'
1 2 3

# 指定分割符号，例如这里指定2
echo -e "1 2 3" | awk -F '2' '{print $1,$2,$3}'
1   3

# 分割符号示例
grep "test" test.log | awk -F ' ' '{print $3}' 
注：按' '分割，输出第三个
注：awk -F ' ' '{print $3}' 等价于 awk '{print $3}'


# 可以定义一些变量
echo | awk '{ var1="v1"; var2="v2"; var3="v3"; print var1,var2,var3; }' 
v1 v2 v3


# 支持运算符 = += -= *= /= %= ^= **=
awk 'BEGIN{a="b";print a++,++a;}'
0 2


# $0,$1, $2表示字段的位置
# 当然也支持从后访问，例如：$NF最后一个字段，$(NF-1)倒数第二个
# NR表示行号
echo -e "line1 f2 f3\nline2 f4 f5\nline3 f6 f7" | awk '{print "Line No:"NR", No of fields:"NF, "$0="$0, "$1="$1, "$2="$2, "$3="$3, "NF最后一个="$NF, "NF-1倒数第二个="$(NF-1), "NR行数="NR}'
Line No:1, No of fields:3 $0=line1 f2 f3 $1=line1 $2=f2 $3=f3 NF最后一个=f3 NF-1倒数第二个=f2 NR行数=1
Line No:2, No of fields:3 $0=line2 f4 f5 $1=line2 $2=f4 $3=f5 NF最后一个=f5 NF-1倒数第二个=f4 NR行数=2
Line No:3, No of fields:3 $0=line3 f6 f7 $1=line3 $2=f6 $3=f7 NF最后一个=f7 NF-1倒数第二个=f6 NR行数=3


# 支持正则，~：匹配正则表达式  !~：不匹配正则表达式。注意：
awk 'BEGIN{a = "abc"; if (a ~ /^a/){print "匹配成功";}}'
匹配成功


# 支持if-else、while、do-while、for
awk 'BEGIN{
test=100;
if(test>90){
  print "very good";
  }
  else if(test>60){
    print "good";
  }
  else{
    print "no pass";
  }
}'

very good
------------------------------------------------------------------------------------------------------------------------
awk 'BEGIN{
test=100;
total=0;
while(i<=test){
  total+=i;
  i++;
}
print total;
}'
5050
------------------------------------------------------------------------------------------------------------------------
awk 'BEGIN{
for(k in ENVIRON){
  print k"="ENVIRON[k];
}

}'
TERM=linux
G_BROKEN_FILENAMES=1
SHLVL=1
pwd=/root/text
...
logname=root
HOME=/root
SSH_CLIENT=192.168.1.21 53087 22



# 支持break，continue的操作，不过awk里是next
cat text.txt
a
b
c
d
e
awk 'NR%2==1{next}{print NR,$0;}' text.txt
2 b
4 d


# 支持将结果输入输出
echo | awk '{printf("hello word!n") > "datafile"}'
# 或
echo | awk '{printf("hello word!n") >> "datafile"}'


```



#### **小结**

awk是一种编程语言，拥有编程语言基本的特性，例如：变量、函数、循环、条件语句、正则、数组。



#### xargs

给其他命令传递参数的一个过滤器。（在awk下插多一条命令，主要是方面下面实战解释。）

```
假设一个命令为 sk.sh 和一个保存参数的文件 arg.txt：
#!/bin/bash
#sk.sh 命令内容，打印出所有参数。
echo $*
--------------------------------------------------------
arg.txt 文件内容：
cat arg.txt
aaa
bbb
ccc
--------------------------------------------------------
cat arg.txt | xargs -I {} ./sk.sh -p {} -l
-p aaa -l
-p bbb -l
-p ccc -l
```



#### **实战**

##### **关闭服务**

示例：

```
ps -ef | grep 服务名 | grep -v "grep\|bash" | awk '{print $2}' | xargs kill -9
```

解读：

1. | 表示管道
2. ps -ef后面解释（以system v方式列出当前全部运行的进程），grep找到对应的服务信息。
3. grep -v反转查找，这样就能到对应服务的所在的行。
4. 接着用awk进行分割，默认是按照空格分割，$2表示第二个字段。
5. xargs 一般是和管道一起使用，给其他命令传递参数的一个过滤器
6. kill -9强制终止程序



#####  **查看最繁忙的线程**

示例：

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');
threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');
sudo /usr/local/src/jdk1.8.0_152/bin/jstack $processid | grep "0x$threadid" -A100 --color;

注意：sudo $JAVA_HOME/bin/jstack $processid | grep "0x$threadid" -A10 --color;
```

解读：

1. top -bn 1查看耗时高的进程（解释见top），grep找到java程序，head -n1取第一行，接着使用awk进行分割取第一个字段。结果用变量processid保存。
2. 后面top -Hbn 1 -p同理，找到对应的进程耗时高的子线程，并且以十六禁止打印，结果用变量threadid保存。
3. 最后用jstack命令查看



**注意：**

- 不加sudo会报**"Could not attach to "**，原因是：进程启动用户和自己jstat的用户不是同一个，使用sudo即可解决。
- 单独执行sudo会报：**"命令 `sudo` 是被禁止的 ..."**，但是上面的语句连接在一起执行就不会报，猜测是会当成脚本而不是单独的命令行。
- 如果配置了环境变量，可用$JAVA_HOME找到对应的java目录。例如：查看jdk内置的bin命令，`ls $JAVA_HOME/bin`



##### **查看最繁忙进程的GC情况**

示例：

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo /usr/local/src/jdk1.8.0_152/bin/jstat -gc $processid 3000 2;

等价于（替换了$JAVA_HOME）
processid=$(top -bn 1 | grep java | head -n1 | awk '{print $1}');
sudo $JAVA_HOME/bin/jstat -gc $processid 3000 2;
```

同上，只不过这里用的jstat -gcutil命令，同理，还可以使用jmap、jinfo等。



#### 对文件内容做统计

示例：

```
awk '{print $1, $4}' netstat.txt 

awk '$1 =="tcp" && $2==1{print $0}' netstat.txt 

awk '{arr[$1]}END{for(i in arr)print i"\t" arr[i]}'
```



### sed

https://wangchujiang.com/linux-command/c/sed.html

介绍：stream editor，功能强大的流式文本编辑器

#### 批量替换文件内容

示例：

- -i 匹配并写入替换
- g命令：全部替换
- d命令：删除

```
sed 's/book/books/' file

sed -i 's/book/books/' file

# 全部替换
sed -i 's/book/books/g' file

# 删除空白行
sed '/^$/d' file
```



### find

介绍：find命令可以根据给定的路径和表达式查找的文件或目录，支持正则。

#### 查找特定文件

- -name：按照名称查找
- -iname：忽略大小写

示例：

```
find -name "test"

find / -name "test"

find ~ -name "target*"
find ~ -iname "target*" # 忽略大小写
```



## 服务相关命令

### top

当然除了查看文件，我们可能还需要看一些进程

示例：top

```
top - 16:10:57 up 59 days, 48 min,  6 users,  load average: 0.00, 0.03, 0.00
Tasks: 225 total,   1 running, 224 sleeping,   0 stopped,   0 zombie
Cpu(s):  1.5%us,  1.3%sy,  0.0%ni, 97.1%id,  0.0%wa,  0.0%hi,  0.0%si,  0.1%st
Mem:  16333704k total, 15213112k used,  1120592k free,   490716k buffers
Swap:  2097144k total,   379596k used,  1717548k free,  3069352k cached
```

注意：最上面回展示一个load average: 0.00, 0.03, 0.00，下面会介绍。

zombie：僵尸进程，如果不是0就需要注意了。



### 僵尸进程

https://www.jianshu.com/p/981f2bac7c80

介绍：完成了生命周期但却依然留在进程表中的进程。

产生原因：父进程未能读取到子进程的 **Exit** 信号，则这个子进程虽然完成执行处于死亡的状态，但不会从进程表中删掉



**如何杀掉僵尸进程？**

正常情况下我们可以用 SIGKILL 信号来杀死进程，但是僵尸进程已经死了， 你不能杀死已经死掉的东西。 因此你需要输入的命令应该是

```
kill -s SIGCHLD pid 或者 kill -17 pid 
# 注SIGCHILD对应的17，下面kill命令会解释信号
```



#### **线程模式 -H**

-H：（Threads mode）设置线程模式，不带-H显示的是总和，带-H显示的内容更多。

> Instructs top to display individual threads.  Without this command-line option a summation of all threads in each process is  shown.   Later  this  can  be changed with the `H' interactive command.
>
> 指示top显示单个线程。如果没有这个命令行选项，将显示每个进程中所有线程的总和。稍后可以用交互命令H更改

示例

```
top -H
```



#### **屏幕刷新间隔时间 -d**

-d：（Delay time）改变显示的更新速度，单位s。【或是在交互式指令列( interactive command)按 d或s，然后输入对应的数字。】

示例：

```
top -d 5
```



#### **循环显示的次数 -n**

-n：（Number-of-iterations）设置更新次数

示例：

```
top -n 1
```



#### **以批处理模式操作 -b**

-b：（Batch-mode operation）以批处理模式操作。在此模式，可进行输出。

>  Starts top in Batch mode, which could be useful for sending output from top to other programs or to a file.  In this mode, top will not  accept  input  and runs until the iterations limit you've set with the `-n' command-line option or until killed.

示例：

```
top -b
```



#### **显示进程信息 -p**

-p：(Monitor-PIDs mode) 指定进程，显示进程信息

示例：

```
top -p pid
```



#### **交互模式**

在top命令执行过程中可以使用的一些交互命令。



- h：显示帮助画面，给出一些简短的命令总结说明【常用】
- q：退出程序，**Ctrl+C停止太low了，试试按q直接退出。**【常用】
- d或s：屏幕刷新间隔时间，单位s。输入0值则系统将不断刷新，默认值是3s【常用】
- 1：数字1能够查看CPU信息，当然也可以看到是几核 【常用】

- M：（Memory）根据驻留内存大小进行排序【常用】
- P：（CPU）根据CPU使用百分比大小进行排序【常用】
- T：（Time）根据时间/累计时间进行排序【常用】
- w：将当前设置写入~/.toprc文件中
- l：切换显示平均负载和启动时间信息
- m：切换显示内存信息
- t：切换显示进程和CPU状态信息



#### **实战**

示例：

```
top -Hbn 1 | grep java
top -Hp
top -Hn 1 

top 按键：M P T 通常我们会查看占用内存或者CPU，看看服务负载是否高
```



### free、df、du显示磁盘的相关信息

#### free

free：显示系统中可用和可用的**物理内存和交换内存**的总量，以及内核使用的缓冲区和高速缓存。

-  -b：（bytes）以Byte为单位显示内存使用情况
-  -k：（kilo）以KB为单位显示内存使用情况
- -m：（mega）以MB为单位显示内存使用情况
-  -g：（giga）以GB为单位显示内存使用情况
- -h：（human）以对人类友好的方式显示内情况【常用】

示例：

```
[app@test ~]$ free -h
             total       used       free     shared    buffers     cached
Mem:           15G        14G       447M       652K       139M       5.0G
-/+ buffers/cache:       9.7G       5.6G
Swap:          15G        99M        15G
```



#### df

df：（file system disk）可以显示目前所有文件系统的**磁盘**可用空间及使用情形

- -h：（human）以对人类友好的方式显示内情况【常用】
- -l或--local：仅显示本地端的文件系统（注：比如像我们挂载的u盘就不属于本地端了）【常用】

示例：

```
[app@test ~]$ df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/xvda3            132G   65G   61G  52% /
tmpfs                 7.6G     0  7.6G   0% /dev/shm
/dev/xvda1            190M   27M  154M  15% /boot
172.16.3.211:/opt/my_mount
                      1.5T  945G  497G  66% /usr/local/my_mount
[app@test ~]$ df -lh
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda3      132G   65G   61G  52% /
tmpfs           7.6G     0  7.6G   0% /dev/shm
/dev/xvda1      190M   27M  154M  15% /boot
```



#### du

du：（disk usage）查询**文件或文件夹**的**磁盘**使用空间

- -h：（human）以对人类友好的方式显示内情况【常用】
- --max-depth 查看下级文件下

示例：

```
df -h 
du -h --max-depth=1 ./

[app@4px5-28 ~]$ du -h
4.0K    ./.ansible/tmp
8.0K    ./.ansible
8.0K    ./.ssh
8.0K    ./.oracle_jre_usage
96K     .
```



#### 总结

常用 `free | df -h`



### uptime 查看系统负载信息

https://www.cnblogs.com/rexcheny/p/9382396.html

查看系统负载信息。（当然也可以用top或w命令查看，前面介绍过）

示例：

```
[root@test:/root] uptime
 16:04:29 up 59 days, 42 min,  6 users,  load average: 0.03, 0.05, 0.01
 # load average 系统在过去的1分钟、5分钟和15分钟内的平均负载
 # 应该关注5分钟或者15分钟，因为CPU偶尔高一些比较正常，但是如果最近15分钟都很高就需要调查了
```



**什么是系统平均负载？**

系统平均负载是指在特定时间间隔内运行队列中的平均进程数，见下图。

简单来说：有多少核心即为有多少负载（load）

> DESCRIPTION
>        uptime  gives  a one line display of the following information.  The current time, how long the system has been running, how
>        many users are currently logged on, and the system load averages for the past 1, 5, and 15 minutes.
>
>        This is the same information contained in the header line displayed by w(1).
>             
>        System load averages is the average number of processes that are either in a runnable or uninterruptable state.   A  process
>        in  a  runnable  state is either using the CPU or waiting to use the CPU.  A process in uninterruptable state is waiting for
>        some I/O access, eg waiting for disk.  The averages are taken over the three time intervals.  Load averages are not  normal‐
>        ized  for the number of CPUs in a system, so a load average of 1 means a single CPU system is loaded all the time while on a
>        4 CPU system it means it was idle 75% of the time.

![img](images/load.png)



**CPU高不等同于load高，load高不等于CPU高**

CPU负载高利用率低：说明等待的任务多

CPU利用率高负载低：说明任务少，但是任务执行时间长



**理想的CPU load是多少？**

理想情况下一个核心被一个进程占用，即：LOAD不应该超过最大核心数量

【应该关注5分钟或者15分钟，因为CPU偶尔高一些比较正常，但是如果最近15分钟都很高就需要调查了】



### w 

显示目前登入系统的用户信息

> Show who is logged on and what they are doing

示例：

```
w
```



### watch

可以将命令的输出结果输出到标准输出设备，多用于周期性执行命令/定时执行命令

- -t或-no-title：会关闭watch命令在顶部的时间间隔,命令，当前时间的输出。

示例：

```
watch -t uptime
```



### ps

当前处理程序的快照报告

> report a snapshot of the current processes.

- -e：显示所有程序，和-A效果一样

- -f：（Do **full**-**format** listing），显示所有格式列表，例如：UID,PPIP,C与STIME栏位

- -a：显示与终端无关的程序

  - >  Select all processes except both session leaders (see getsid(2)) and processes not associated with a terminal

- -u：列出属于该用户的程序的状况

- x：显示所有程序，不以终端机来区分

  - > Lift the BSD-style "must have a tty" restriction, which is imposed upon the set of all processes when some BSD-style (without "-") options are used or
    >               when the ps personality setting is BSD-like.  The set of processes selected in this manner is in addition to the set of processes selected by other
    >               means.  An alternate description is that this option causes ps to list all processes owned by you (same EUID as ps), or to list all processes when used
    >               together with the a option.

示例：

```
ps -ef 【常用】 注：为什么不用ps -Af，猜测是ef更方便记忆

ps -aux

ps -ef | grep init 查找当前运行进程中的的init进程【常用】

```

>     注：以system v方式列出当前全部运行的进程
>
>     To see every process on the system using standard syntax:
>           ps -e
>           ps -ef
>           ps -eF
>           ps -ely
>
>     注：以BSD方式列出当前全部运行的进程
>
>     To see every process on the system using BSD syntax:
>           ps ax
>           ps axu



### kill

发送信号到进程

- -a：当处理当前进程时，不限制命令名和进程号的对应关系
- -l <信息编号>：若不加<信息编号>选项，则-l参数会列出全部的信息名称【常用】
- -p：指定kill 命令只打印相关进程的进程号，而不发送任何信号
- -s <信息名称或编号>：指定要送出的信息
- -n：\<SIG\>信号名称对应的数字
- -u：指定用户



#### 列出信号名称

平时你可能用过`kill -9`，但是否知道其意思。-9其实是对应一个信号名称，你可以用以下命令查询。

示例：

```
[root@test:/root] kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```

说明：

HUP     1    终端挂断 

INT     2    中断（同 Ctrl + C）

QUIT    3    退出（同 Ctrl + \） 

KILL    9    强制终止 【常用】

TERM   15    终止 

CONT   18    继续（与STOP相反，fg/bg命令） 

STOP   19    暂停（同 Ctrl + Z）



#### 实战

- -9：强制终止 【常用】

示例：

```
kill -9

[root@test ~]# sleep 90 &
[1] 24509
[root@test ~]# kill -9 24509

# ----------------------------------------------------------------------------------------------------------------------------------------
stop(){
    echo "关闭服务中..."
    # decode选择要关闭的服务 对应: spring.application.name
    ps -ef | grep decode | grep -v "grep\|bash" | awk '{print $2}' | xargs kill -9
}
```



### 上传下载文件

有时候可能需要上传下载文件，可以用下面两个命令

- rz：上传文件命令
- sz：下载文件到本地

示例：

```
rz 
sz test.txt
```



### ping

测试主机之间网络的连通性，执行ping指令会使用ICMP传输协议，将ICMP ECHO_REQUEST数据包发送到网络主机。

- -c<完成次数>：设置完成要求回应的次数【常用】
- -i<间隔秒数>：指定收发信息的间隔时间，单位秒【常用】

示例：

```
ping bing.com
ping -c5 bing.com
ping -i2 bing.com
```



### curl

利用URL规则在命令行下工作的文件传输工具，理解为命令行的请求

- -X/--request：指定什么命令，例如：POST、GET
- -H/--header：自定义头信息传递给服务器
- -d/--data：HTTP POST方式传送数据

示例：

```
curl http://127.0.0.1:8080/test -X POST -d '{"code":"test"}' -H "Content-Type:application/json"
```



### traceroute

显示数据包到主机间的路径【常用于排查网络问题】

traceroute跟踪从IP网络获取到给定主机的**路由信息包**。它利用IP协议的生存时间（TTL）字段并尝试从每个网关到主机的路径引发ICMP TIME_EXCEEDED响应。

示例：

```
traceroute bing.com

```

说明：

- 记录按序列号从1开始，每个记录就是一跳 ，每跳表示一个网关，每行有三个时间，单位是ms
- 如果延时比较长，可能原因：网关阻塞、DNS出现问题导致不能解析主机名、域名



### wget

https://wangchujiang.com/linux-command/c/wget.html

Linux系统下载文件工具，支持HTTP，HTTPS和FTP协议，以及通过HTTP代理进行检索。

- -b：-background 启动后转入后台执行
- -i：-input-file=FILE 下载在FILE文件中出现的URLs

示例：

```
# 单个下载
wget http://www.jsdig.com/testfile.zip

# 后台下载
wget -b http://www.jsdig.com/testfile.zip

# --------------------------------------------------------------------------------------------------------------------------------------------------------------
# 下载多个文件
wget -i filelist.txt
首先，保存一份下载链接文件：
cat > filelist.txt
url1
url2
url3
url4
```



### lscpu

显示有关CPU架构的信息

示例：

```
lscpu
```



## 查看CPU个数

方式多种多样，个人推荐是第一种

```
方式一：
top 然后按照1


方式二：
cat /proc/cpuinfo | grep processor

方式三：
lscpu

```



## 总结

实战出真知，常用的命令都在上面了，在实际中能解决大部分问题。