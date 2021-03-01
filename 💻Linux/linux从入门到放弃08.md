---
title: linux从入门到放弃——08ubuntu17.10Can't login
date: 2017-8-25 23:55:43
categories: Linux
comments: true
---
# linux从入门到放弃——08ubuntu17.10Can't login

Today is 2018-4-16, yesterday I uninstalled my windows10 and installed the ubuntu17.10.

You can use the command **'lsb release -a**' to see the current version of your linux.

When I installed my linux, then I configured my development enviroment , I 'vim /etc/profile' to change something.

It's saddened for me,when reboot I attempt to login but failed. I'm sure my password is right.it's seem to login, but it still stays window for logining.

Then I use 'Ctrl+Alt+F1~F6' to terminal(tty:Teletype) to modify /etc/profile.

But it shows  like that"command 'xxx' is available in bin.". So what? what should you do to modify.

In linux, when we input a command in terminal, it will do something. what something doing when we press 'Enter'.

Factly, system actually launch a program. for example, like'cd pwd ls'. In system, use the 'PATH' appoint command's directory.when we use command then system first will search corresponding 

command from 'PATH'.

so "command 'xxx' is available", we should use the absolutely path.
```
/usr/bin/sudo /usr/bin/vim /etc/profile
```
then modify something wrong of setting, use'Ctrl+Alt+F7' return graphical interfaces to login.



载转[Ubuntu Linux 环境变量PATH设置](https://blog.csdn.net/witsmakemen/article/details/7831631)
linux系统环境变量配置文件：

/etc/profile : 在登录时,操作系统定制用户环境时使用的第一个文件 ,此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行。

/etc /environment : 在登录时操作系统使用的第二个文件, 系统在读取你自己的profile前,设置环境文件的环境变量。

~/.profile :  在登录时用到的第三个文件 是.profile文件,每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。

/etc/bashrc : 为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

~/.bashrc : 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。



PASH环境变量的设置方法：

方法一：用户主目录下的.profile或.bashrc文件（推荐）

登录到你的用户（非root），在终端输入：

**\$ sudo gedit ~/.profile(or .bashrc)**

可以在此文件末尾加入PATH的设置如下：

**export PATH=”$PATH:your path1:your path2 ...”**

保存文件，注销再登录，变量生效。

该方式添加的变量只对当前用户有效。



方法二：系统目录下的profile文件（谨慎）

在系统的etc目录下，有一个profile文件，编辑该文件：

**\$ sudo gedit /etc/profile**

在最后加入PATH的设置如下：

**export PATH=”$PATH:your path1:your path2 ...”**

该文件编辑保存后，重启系统，变量生效。

该方式添加的变量对所有的用户都有效。



方法三：系统目录下的 environment 文件（谨慎）

在系统的etc目录下，有一个environment文件，编辑该文件：

**$ sudo gedit /etc/environment**

找到以下的 PATH 变量：

PATH="<......>"

修改该 PATH 变量，在其中加入自己的path即可，例如：

**PATH="<......>:your path1:your path2 …"**

各个path之间用冒号分割。该文件也是重启生效，影响所有用户。

注意这里不是添加export PATH=… 。



方法四：直接在终端下输入

**\$ sudo export PATH="$PATH:your path1:your path2 …"**

这种方式变量立即生效，但用户注销或系统重启后设置变成无效，适合临时变量的设置。

注意：方法二和三的修改需要谨慎，尤其是通过root用户修改，如果修改错误，将可能导致一些严重的系统错误。因此笔者推荐使用第一种方法。另外嵌入式 Linux的开发最好不要在root下进行（除非你对Linux已经非常熟悉了！！），以免因为操作不当导致系统严重错误。

下面是一个对environment文件错误修改导致的问题以及解决方法示例：



问题：因为不小心在 etc/environment里设在环境变量导致无法登录

提示：不要在 etc/environment里设置 export PATH这样会导致重启后登录不了系统

解决方法：

在登录界面 alt +ctrl+f1进入命令模式，如果不是root用户需要键入（root用户就不许这么罗嗦，gedit编辑会不可显示）

/usr/bin/sudo /usr/bin/vi /etc/environment then dd export PATH

