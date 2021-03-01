---
title: linux从入门到放弃——03细说软件安装与vim大法
date: 2017-8-8 16:18:43
categories: Linux
comments: true
---

# linux从入门到放弃——03细说软件安装与vim大法

上一次我们说到了如何安装linux以及了解linux的设计思想，这对我们学习linux都是非常重要的。如果在安装软件遇到问题而卸载的同学想必是不会看到这篇文章了。我们先来说点，怎么学习linux，首先要有信心，不要遇到问题就退缩放弃了，遇到问题得学会思考和动手解决，其次是要选择合适的教程，找本书和视频学习，书本入门推荐是《鸟哥的私房菜》。最后是坚持下去，多去看社区和官方文档。其实学习没什么途径，如果说途径也只是说有老司机带，可是对于很多老司机来说不可能说一点一点的告诉你，一般都是告诉你一个方向，很多人都是自己慢慢摸索来着。所以没什么捷径，就是自己多研究，这也是我写下linux系列的目的，一个是便于我自己思考，另一个就是把我学习过程的一个经历写下分享给大家，希望有点帮助吧。恩，下面说说怎么安装软件吧。

<br>
### 阅读说明
阅读说明部分开始没看懂没关系，随便看看，然后看完安装方式回头再看看。这些命令可能你还不了解，我下一篇将会介绍，我们一步一步来。首先了解linux安装软件的原理。

Linux系统中,软件通常以**源代码**或者**预编译包**的形式提供。

1. 软件源代码需要编译为二进制的机器代码才能够使用,安装比较耗时,不过您可以自行调节编译选项,决定需要的功能或组件,或者针对硬件平台作一些优化。

2. 预编译的软件包,通常是由软件的发布者进行编译,您只要将软件拷贝到系统中就可以了。考虑到预编译软件包的适用性,预编译软件包通常不会针对某种硬件平台优化。它所包含的功能和组件也是通用的组合。


一般来说，当我们装了linux之后，第一步不是安装中文输入法，或者配置环境这些，不是做别的，就是选择合适的源，按照下面顺序打开。
```
System Settings——Software&Updates——Ubuntu Software——Download from——Select Best Server——Choose Server
```
我这里选择的是“http：//mirrors.ustc.edu.cn/ubuntu”

<img src="images/linux/updates.png" width="500px" align="center"/>

然后打开我们的终端（Ctrl+Alt+T）更新源。

```
$ sudo apt-get update
$ sudo apt-get upgrade  
```
如果你不更新的话，在你用apt-get方式安装的时候很可能就会出现下面的安装错误提示：
```
E: Unmet dependencies. Try 'apt-get -f install' with no packages (or specify a solution)

```
原因：列软件包具有未满足的依赖关系，非正常停止apt-get install。

解决方法：

```
$ sudo apt-get -f（sudo apt --fix-broken install修复受损安装包）
$ sudo apt-get update
$ sudo apt-get upgrade  
```
说明：f参数为--fix-broken的简写形式，可以在man apt-get 中搜索-f参数查询到其帮助信息。-f参数的主要作用是修复依赖关系（depends），假如用户的系统上有某个package不满足依赖条件，这个命令就会自动修复，安装程序包所依赖的包。（然后在linux中有一句话，出门遇到问题找警察，linux遇到问题找男人man，但是这个其实是manual单词的缩写，说明书，手册的意思）。

- sudo apt-get update：获得最近的软件包的列表，然后列表中包含一些包的信息，比如这个包是否更新过。
- sudo apt-get upgrade ：如果这个包没有发布更新，就不管它；如果发布了更新，就把包下载到电脑上，并安装。

apt-get upgrade 与apt-get upgrade的关系：

由于包与包之间存在各种依赖关系。upgrade只是简单的更新包，不管这些依赖，它不添加包，或是删除包。而upgrade可以根据依赖关系的变化，添加包，删除包。

### 安装方式
下面介绍在ubuntu中安装软件的六种方式：apt-get、dpkg安装deb、make install安装源码包、二进制包的安装方式、rpm包的安装方式  、其他方式。

（sudo这个我们暂时不解释，你可以理解为提升权限的意思，预设为root，root就是我们的最高权限因为之前也说了普通用户不能做所有的事情）

#### 1.apt-get

Ubuntu有许多软件源，使用**apt-get install 软件名**是比较常见的一种方法。apt-get：apt-get命令是Debian Linux发行版中的**APT软件包管理工具**。

比如安装build-essential

我们打开终端，输入：

```
$sudo apt-get install build-essential
```
执行后问你
```
Do you want to continue [Y/n]? y
```
我们当然是选择安装了。

然后我们说说apt-get的的各种参数：

- apt-get install xxx ：安装xxx（如果带有参数，那么-d 表示仅下载 ，-f 表示强制安装 ）
- apt-get remove xxx  ：卸载xxx  
- apt-get update      ：更新软件信息数据库  
- apt-get upgrade     ：进行系统升级  
- apt-cache search    ：搜索软件包  
  提示：建议您经常使用“apt-get update”命令来更新您的软件信息数据库 



#### 2.ubuntu软件包格式为deb

deb是debian系Linux的包管理方式,ubuntu是属于debian系的Linux发行版,所以默认支持这种软件安装方式。
```
$sudo  dpkg  -i  package.deb
```
或者直接双击安装，不建议。

这里的pkg是package的意思。

下面简单列了几个dpkg命令：

- dpkg -i package.deb 	安装包
- dpkg -r package 		删除包
- dpkg -P package 		删除包（包括配置文件）
- dpkg -L package 		列出与该包关联的文件
- dpkg -l package 		显示该包的版本
- dpkg –unpack package.deb解开 deb 包的内容
- dpkg -S keyword 		搜索所属的包内容
- dpkg -l 			列出当前已安装的包
- dpkg -c package.deb 	列出 deb 包的内容
- dpkg –configure package 配置包

根据Ubuntu中文论坛上介绍，使用apt-get方法安装的软件，所有下载的deb包都缓存到了/var/cache/apt/archives目录下了，所以可以把常用的deb包备份出来，甚至做成ISO工具包、刻盘，以后安装Ubuntu时就可以在没有网络环境的情况下进行了。



#### 3.make install源代码安装

如果用源代码编译安装前,需要先建立编译环境,($sudo apt-get install build-essential )。在linux中很多软件只提供了源代码给你,需要你自己进行编译安装,一般开源的软件都会使用tar.gz压缩档来进行发布,当然也有其他的形式。ubuntu支持ZIP,TAR.GZ为后缀的压缩文档，对于RAR文件，一般通过默认安装的ubuntu是不能解压rar文件的，只有在安装了rar解压工具之后，才可以解压。

然后解压到/tmp目录下,在解压的目录内打开Terminal（终端），使用命令”ls“看看有没有configure这个文件。

然后源码安装大致可以分为三步骤：

```
配置（$sudo ./configure）–＞ 编译（$sudo make） –＞ 安装（$sudo make install）。
```

配置：这是编译源代码的第一步，通过 ./configure 命令完成。执行此步以便为编译源代码作准备。常用的选项有 --prefix=PREFIX，用以指定程序的安装位置。例如：./configure --prefix=/usr/local/nagios 

编译：一旦配置通过，就可以用 make 指令来执行源代码的编译过程。不用软件编译所需的时间也各有差异，我们所要做的就是耐心等候和静观其变。这里虽然仅下简单的指令，但有时候所遇到的问题

却十分复杂。

安装：如果编译没有问题，那么执行 sudo make install 就可以将程序安装到系统中了。



#### 4. 二进制包的安装方式

有不少不开源的商业软件都会采用这种方式发布Linux软件,例如google earth,拿到二进制软件后,把它放到/tmp目录,在终端下进入安装目录,在安装目录下执行:
```
./软件名
```

然后按照一步步提示,就能安装该软件。例如安装realplayer播放器:你直接到官网 http://www.real.com/linux 下载 RealPlayer 的安装包,安装包是 .bin 格式,用如下命令安装:

chmod +x RealPlayer11GOLD.bin

./RealPlayer11GOLD.bin    



#### 5.rpm包的安装方式    

rpm包是deb包外最常见的一种包管理方式,但ubuntu同样可以使用rpm的软件资源。首先我们需要安装一个rpm转deb的软件
```
sudo apt-get install alien
```
然后就可以对rpm格式的软件转换成deb格式了:
```
sudo alien -d package.rpm
```
然后就可以用deb的安装方式进行软件安装。也可以不需转换而直接对rpm包进行安装:
```
alien -i *.rpm
```
更多的alien使用方法可以用-h参数查看相应说明文档。



#### 6.其他方式

其他安装方式一般还有脚本安装方式。**这类软件你会在软件安装目录下发现类似后缀名的文件,如: .sh .py .run等等,有的甚至连后缀名都没有,直接只有一个INSTALL文件。**对于这种软件,可尝试以下几种

方式安装:最简单的就是直接在软件目录下输入: ./软件名* (注意有一个*号,那是一般可以通配所有后缀名)或者 : sh 软件名.sh或者: python 软件名.py)



### 总结

一般来说，我们一般是用apt-get这种方式，如果其他方式，你下载解压后要看看是上面哪一种形式，就用哪一种方式，如果不行的话，通常里面会有个readme的文件，查看里面的说明教程就可以了。  
好了，上面几种方法其实已经够你用了，那么你可以到官网上下载安装中文输入法了。在这之前你还需要安装一个叫做fcitx的东西，具体就不做解释，这个就相当于你安装这个东西需要先安装另一个东西作为环境。比如说在windows下你要安装eclipse，首先就要有JDK，因为这个eclipse是java写的，要想进行编译和解释的话就要有这个开发环境。我觉得现在你已经会安装了，那么你就可以把你在windows下的一些必备的软件安装在上面了。安装完后是不是觉得linux顺手了一点。但是有一点的是QQ是Linux中文用户面对的问题之一，一般情况下不推荐使用独立软件版本登录QQ，最合适的方式是使用[WebQQ](http://web.qq.com)


***********************

好了，如果说linux就这样的话当然还不如windows好用，你看在windows还可以玩各种游戏多舒服，操作简单又方便。那这个linux肯定是有自己的特点，首先来说，linux是完全免费开源的，就是说你可以随便修改源代码，像windows就不是开源的，我们不能修改，只能通过他的接口去调用一些东西。其次linux是非常安全的系统，之前也说了linux是以文件的形式，这样就可以对其设置权限，你不能随便查看和修改一些文件。再有就是linux支持多用户，多任务。各个用户对于自己的文件设备有自己特殊的权利，保证之间互不影响。多任务则使得linux多个程序同时并独立地运行。linux其他特点，例如完全兼容POSIX1.0标准，良好的桌面环境等，支持多种硬件平台。其中比较主流的是的GNOME桌面环境，即GNU网络对象模型环境 (The GNU Network Object Model Environment)，GNU计划是开放源码运动的一个重要组成部分。目标是基于自由软件，为Unix或者类Unix操作系统构造一个功能完善、操作简单以及界面友好的桌面环境，他是GNU计划的正式桌面。linux主要操作其实是以命令的形式，所以如果不懂linux的命令其实不算是了解linux。
<br>
我们就以用C语言编写运行一个“hello world”来介绍认识一些命令。首先介绍几个简单的命令，我们先打开终端（Terminal）

<img src="images/linux/terminal.png" align="center" width=“400px”/>



snowhotarubi@linux:~$ 

那我们来做点什么呢，首先呢，我们来看看我们在哪里，就可以用**pwd**这个命令，英文就是“print work directory”。我的英文不好，但是记这些命令我还是用理解缩写的意思去记，命令真的很多，我也没有什么好的方法，我觉得就是多用养成习惯就不会忘记了，有好方法的话我还是愿意听一下。然后一些命令其实我们不用非要记住，用到的时候能够马上找到就可以了。
<img src="images/linux/show1.png" align="center"/>

好了，接下来我们可以看看这个目录下有什么，就用**ls**的命令。如果我们想看详细的信息就可以用**ls -l**。

接下来，我们如果想要查看上一级目录就可以用**cd ..**，这个cd就是改变目录的意思，而“.."表示上一级，”.“表示当前目录，那下一级怎么办呢？ 比如我要进入home，这个时候就可以用 ”cd home/“(输入前面部分，可以用tab键补全）。

我们发现$符号前面的字符变了，就是我们的/，说明前面就是表示我们当前的路径，相对于/home/snowhotarubi，这里有个绝对路径和相对路径的概念，简单来说，像cd /home/snowhotarubi/Desktop这种就是以绝对路径方式进入，而cd Desktop就是相对路径的形式。如果我们在这时候cd Desktop是会显示No such file or directory，这里因为相对路径是相对于当前目录，而绝对路径是绝对于根目录”/“。

这个时候我们来创建一个hello.c文件吧，**mkdir:建立空目录  touch:新建空文件**

例如：**snowhotarubi@linux:~\$touch hello.c**

那么问题又来了，我们怎么用命令进行修改或者编辑文件，我们都知道双击一个编辑器我们就可以编辑东西，进行保存和修改，删除这些基本操作。虽然在linux中也有文本编辑器，但是在终端里人们认可的通用的基本就是vim编辑器，我安装的第一个软件也是vim。前面我们已经说过了安装方法了**sudo apt-get install vim**。安装完后，

**snowhotarubi@linux:~$vim helloc.c**

 (你会发现你进入了一个神奇的世界，但是什么都不能做，你怎么敲打好像都没有输入的反应)

这时候，你如果在英文状态下按下键盘上的a/i（add/insert）就发现可以进行编辑了，你会发现下面出现了一个--INSERT--(或是--插入--)，这就是插入模式，接着我们熟练的写下了:

```
#include<stdio.h>
int main()
{
    printf("Hello World!\n");
    return 0;
}
```
<img src="images/linux/show2.png" align="center"/>

然后就是怎么退出保存的问题了，通常我们在学编程的时候直接用IDE，直接在里面新建一个项目，新建源文件编写就可以直接点击运行。在这里，vim编辑器有几种退出方式，首先要按一下ESC退出命

令，进入命令模式，通常我们把vim分为三种模式：命令模式，输入模式（编辑/插入模式），末行模式。，接着我们输入":"这个分号，我们就发现光标移到了下面，并且出现一个":"等待我们输入，这就是

我们说的末行模式。我这里因为没有权限所以会显示只读，不能编辑，这个权限问题我们后面会说到，退出方式：

1. :q  （退出不保存，通过会出现警告，“q！”则表示强制推出）
2. :wq(退出并保存）
3. ：x (退出保存）
其实vim进入输入模式有几种方式：i（insert）、a（add）、o（open）、c（change）、r（取代命令）、s（替换命令）
接着我们用gcc编译一下，这时候用ls命令我们就可以看到当前目录下有个a.out的文件，运行它，用名“./a.out”,我们之前说过了“.”表示当前路径，如果只是“a.out”的话就会commad not found.你也可以使用'gcc -o hello hello.c'指定输出名.

### vim(visual interpreter IMproved )編輯器
vim是linux下的常用一種編輯器，在vim中有三種模式，常用的幾個快捷鍵．
```
hjkl　上下左右
a: add 將文本添加在光標之後
i: insert將文本插入在光標之前
G　光標移至文件最後一行
nG　光標移至文件第ｎ行．　　１G,nG
0　光標移至首行
$　光標移至行尾
w　光標右移一個字符 
nw光標右移ｎ個字符
複製粘貼撤銷刪除：　ｙ(copy),p(paste),u(undo),d(delete)
d0
dG
dw, ndw 刪除ｎ個單詞
dd, ndd  4dd 刪除ｎ行
```

末行命令模式：
```
進入，打一個冒號:
或者,打一個／

/exp從光標處向前尋找字符串exp
?exp從光標處向後尋找字符串exp
n重複前一搜索命令
N在光標處向方向重複前一搜索命令
:w　寫盤
:w >> file　寫盤到文件file中
:w! file　強行進行寫盤文件file的動作
:q　退出編輯程序
:q!　強行退出編輯程序，同事放棄編輯緩衝區中的內容
:wq　保存並退出
:set nu　設置編輯時顯示行號
:set nonu　設置編輯時不顯示行號
```


好了，vim的话就不介绍太多了，我们可以在终端下直接输入vim查看，这个东西如果你度过了前期痛苦的阶段，也不失为一个很爽的编辑器。前提当然是你花时间去学习，这个软件设计的定位本来就不是针对大众，是对有一些基础的人，帮助提高效率的。你可能会说我用其他编辑器效率比这高百倍，仁者见仁吧，用的好效率当然会提高，也有一些vim的高手，当然像我们这种就不是高手了。但是不管是高手还是我们，如果要用linxu必须学会vim编辑器的基本操作。我们继续说，这时候如果你切换到末行模式输入help的话它有可能跳出来的是英文版本的帮助，在这里可能会有中文翻译，在终端下直接

输入：snowhotarubi@linux:~$vimtutor

（这个tutor就是教程，教导的意思）可能刚开始使用确实很麻烦，比如我们想点到这里编辑发现不行，你会觉得很痛苦，不要觉得设计出来的人很傻，我们应该是想想别人是怎么样的，为什么会这样设计，而不是按照我们的想法是断定别人的想法。当你认真了解使用它后，我觉得你应该会喜欢上它。

可能这几个命令你会觉得无聊，下面列出几个命令，你自己可以尝试敲一下。
```
shutdonw -h now:立刻关机
shutdonw -r now:关机重启
reboot:重启
bc:计算器   （quit：退出）
date:时间
cal:日历
cal 2017
cal 8 2017
exit:退出
cat:查看文件内容
cd:改变目录
pwd:显示当前工作目录
ls:显示目录内容
stat:显示文件信息
ls -l:显示长列表格式
ls -a:显示隐藏文件
.. ：上级目录（如配合cd使用：cd ..这样你就会进入到上一级目录了）
. ：当前目录
clear:清除
mkdir:建立空目录
touch:新建空文件
vim 文件名：新建一个文件并用vim打开。
rmdir：删除空目录（注意是空的)
rm:删除文件和目录
rm -rf：删除所有内容（包括目录和文件）（r：递归，f：强制，就是英文单词force）
```

好了，这一期就介绍到这里，下一次应该会说群组功能，文件的权限和安全。恩，感谢你的阅读！


