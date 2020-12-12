---
title: linux从入门到放弃——10UNIX系统编程
date: 2017-8-25 23:55:46
categories: Linux
comments: true
---

# linux从入门到放弃——10UNIX系统编程

### 一、gcc编译源代码的四个步骤：

    1) gcc -E预处理，编译器将程序的头文件编译进来，还有宏替换。
    2) gcc -S编译，这里主要是检查语法是否错误，无错误则将代码翻译成汇编语言。
    3) gcc -c汇编,把编译阶段成的文件转换成二进制目标代码。
    4) gcc -o链接，把汇编生产的文件链接为可执行文件。


进行验证：

1.1首先这里是源代码
![](images/gcc1.png)



1.2执行过程

预处理过程——>编译过程——>汇编过程——>链接成可执行文件

Pre-Processing——>compiling——>Assemblin——>Linking
![](images/gcc2.png)



1.3我们可以看到在预处理生成的add.i文件中，添加了头文件还有宏替换
![](images/gcc3.png)
![](images/gcc4.png)



1.4再来看看编译生成的文件内容，可以看到就是汇编语言
![](images/gcc5.png)


### 二、gdb调试
要使程序被编译后包含调试信息,只需在编译时用-g选项打开调试信息选项即可.
```
gdb - h
gdb常用调试命令:
file
kill
list
next
step
run
quit
watch
break
make
shell
```
![](images/gcc6.png)

### 三、make程序
在系统开发期间项目中包含多个程序模块,如果其中一个或多个模块被修改过,就必须要保证**编译、链接**的结果是由最新的模块构成.这时就需要一个make功能的程序,完成编译、链接的管理.

UNIX系统中make程序是作为一个命令来执行的,它会对已经修改的模块进行编译,形成最新的**可执行程序代码**.



make程序工作原理:

首先make程序会根据一个名为makefile的文件描述的内容进行工作.因此先编写好makefile之后在使用make命令.

makefile的内容:

**makefile文件中描述的是该项目中所包含的各个程序,以及各程序之间的相互以来关系和用这些程序产生目标文件时所需要执行的命令**
![](images/gcc7.png)