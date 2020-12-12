# Top详解

## top

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



### top内容说明

```
[root@centos/]# top -n 1
top - 11:27:30 up 103 days, 20:37,  1 user,  load average: 0.58, 0.37, 0.34
Tasks: 250 total,   1 running, 249 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.0 us,  4.5 sy,  0.0 ni, 92.4 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 16266204 total, 10070164 free,  4584084 used,  1611956 buff/cache
KiB Swap:  8257532 total,  5128184 free,  3129348 used. 11231468 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                   
 8356 1002      20   0 6508408 156060   1424 S   6.2  1.0 238:48.51 java                                                              
12245 100       20   0 1187304  97936   4052 S   6.2  0.6  17:29.77 beam.smp                                                          
17440 root      20   0  162256   2240   1548 R   6.2  0.0   0:00.01 top                                                               
    1 root      20   0   62892   4588   2584 S   0.0  0.0 119:11.94 systemd 
```

第一行：表示的项目依次为当前时间、系统运行时间、当前系统登录用户数目、1/5/10分钟系统平均负载

(一般来说，LOAD不应该超过最大核心数量。如果持续高于 的话，那么.....仔细的看看到底是那个程序在影响整体系统吧！)



第二行：显示的是所有启动的进程、目前运行、挂起 (Sleeping)的和无用(**Zombie**)的进程。

(比较需要注意的是最后的 zombie 那个数值，如果不是 0 ，嘿嘿！好好看看到底是那个 process 变成疆尸了吧？！)

(stop模式：与sleep进程应区别，sleep会主动放弃cpu，而stop是被动放弃cpu ，例单步跟踪，stop（暂停）的进程是无法自己回到运行状态的)



第三行：显示的是目前CPU的使用情况，包括us用户空间占用CPU百分比、sy 内核空间占用CPU百分比、ni 用户进程空间内改变过优先级的进程占用CPU百分比(中断处理占用)、

**id 空闲CPU百分比**、wa 等待输入输出的CPU时间百分比、（hi,si,st 三者的意思目录还不清楚 ：)



第四行：显示物理内存的使用情况，包括总的可以使用的内存、已用内存、空闲内存、缓冲区占用的内存



第五行：显示交换分区使用情况，包括总的交换分区、使用的、空闲的和用于高速缓存的大小



第六行：显示的项目最多，下面列出了详细解释

- **PID**（Process ID）：进程标示号 ( 每个 process 的 ID )
- USER：进程所有者的用户名 ( 该 process 所属的使用者 )
- **PR**（Priority ）：进程的**优先级**别 ( 程序的优先执行顺序，越小越早被执行 )
- NI（Nice）：进程的优先级别数值 (与 Priority 有关，也是越小越早被执行 )
- VIRT：进程占用的虚拟内存值
- RES：进程占用的物理内存值
- SHR：进程使用的共享内存值
- **S**（Status）：**进程的状态**，其中S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值是负数。
- %CPU：该进程占用的CPU使用率。
- %MEM：该进程占用的物理内存和总内存的百分比。
- TIME＋：该进程启动后占用的总的CPU时间 ( CPU 使用时间的累加 )
- Command：进程启动的启动命令名称，如果这一行显示不下，进程会有一个完整的命令行



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

