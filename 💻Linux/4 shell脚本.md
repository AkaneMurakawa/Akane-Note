# 4 shell脚本



## 启动脚本

```
#version 1.0
#!/bin/bash
source /etc/profile

JAVA_HOME="/usr/local/src/jdk1.8.0_152"

dir="/usr/local/logs/xxx"

logDir="$dir/service-startup.log"

jarfile=/usr/local/xxxx.jar

consul="--spring.cloud.consul.host=127.0.0.1"

JAVA_OPTS="-Xms1024m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=256m -XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=/usr/local/xxxx.hprof"
nohup $JAVA_HOME/bin/java $JAVA_OPTS -jar $jarfile $consul > $logDir 2>&1  &

------------------------------------------------------------------------------------------------------------------------------------------------------
以下是脚本解释：
#version 1.0
#!/bin/bash
source /etc/profile

# 定义变量
JAVA_HOME="/usr/local/src/jdk1.8.0_152"
dir="/usr/local/logs/xxx"
# 用双引号，内容里可以有变量，例如这里的$dir
logDir="$dir/service-startup.log"
jarfile=/usr/local/xxxx.jar
consul="--spring.cloud.consul.host=127.0.0.1"
# 定义变量，设置java的一些启动参数
JAVA_OPTS="-Xms1024m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=256m -XX:+HeapDumpOnOutOfMemoryError 
-XX:HeapDumpPath=/usr/local/xxxx.hprof"

# 语法：nohup command &  解释：表示后台运行。   
# 语法：command > file 2>&1   解释：2>&1 的意思就是将标准错误重定向到标准输出
nohup $JAVA_HOME/bin/java $JAVA_OPTS -jar $jarfile $consul > $logDir 2>&1  &
```



## 关闭服务

```
ps -ef | grep 服务名 | grep -v "grep\|bash" | awk '{print $2}' | xargs kill -9
# | 表示管道
# ps -ef 以system v方式列出当前全部运行的进程，grep找到对应的服务信息。
# grep -v反转查找，这样就能到对应服务的所在的行。
# 接着用awk进行分割，默认是按照空格分割，$2表示第二个字段。
# xargs 一般是和管道一起使用，给其他命令传递参数的一个过滤器
# kill -9强制终止程序
```



## shell运行

```
sh test.sh 
./test.sh
```



## SHELL语法



**关于command > file 2>&1**

- 标准输入（STDIN）－0 默认接受来自键盘的输入
- 标准输出（STDOUT）－1 默认输出到终端窗口
- 标准错误（STDERR）－2 默认输出到终端窗口
- \> 把STDOUT重定向到文件
- 2> 把STDERR重定向到文件
- \>& 把**所有输出**重定向到文件
- \> 文件内容会被覆盖
- set –C 禁止将内容覆盖已有文件,但可追加
- \>| file 强制覆盖

```
ls 2>1测试一下，不会报没有2文件的错误，但会输出一个空的文件1；
ls xxx 2>1测试，没有xxx这个文件的错误输出到了1中；
ls xxx 2>&1测试，不会生成1这个文件了，不过错误跑到标准输出了；
ls xxx >out.txt 2>&1, 实际上可换成 ls xxx 1>out.txt 2>&1；重定向符号>默认是1,错误和输出都传到out.txt了。
```



### &

&表示后台运行



### 单引号和双引号

- 单引号里的任何字符都会原样输出
- 双引号里可以有变量

```
your_name='runoob'
str="Hello, I know you are \"$your_name\"! \n"
echo -e $str
```



### 输入/输出重定向

```
>> 追加
```



### 语法

![image.png](images\shell2.png)

```
# 变量
your_name="qinjx"
echo $your_name
echo ${your_name}

# 只读
myUrl="http://www.google.com"
readonly myUrl
# 删除变量
unset variable_name

# 数组
${数组名[下标]}


# test
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi

# 函数
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}

# case语句
case $变量名 in 
 模式1）
 	命令序列1
 	;;
 模式2）
 	命令序列2
 	;; 
 *）
 	默认执行的命令序列     
 ;; 
esac
```



## 示例



**decode服务启动shell**

```
# |----|home
# |--------|project
# |------------|upload
# |------------|apps
# |----------------decode.hprof
# |----------------decode.jar
# |------------|bak
# |------------startup.log

# upload存放上传的文件，常配合Jekins使用
# apps存放启动的文件
# bak存放备份的文件
# startup.log为服务启动日志
# decode.hprof为内存溢出后自动dump的文件

-------------------------------------------------------------------------------------------------------------------------------------------------------
# 示例

# 根目录，设置统一的路径
dir="/home/project"

# JVM参数设置，可选
JAVA_OPTS="-Xms1500m -Xmx1500m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=$dir/apps/decode.hprof"

# 服务启动日志
logDir="$dir/startup.log"


# 启动服务
start(){
    # decode.jar选择要启动的jar包
    nohup java $JAVA_OPTS -jar $dir/apps/decode.jar  > $logDir 2>&1  &
}

# 关闭服务
stop(){
    echo "关闭服务中..."
    # decode选择要关闭的服务 对应: spring.application.name
    ps -ef | grep decode | grep -v "grep\|bash" | awk '{print $2}' | xargs kill -9
}

# 更新文件
updateFiles(){
    mv $dir/apps/* $dir/bak/
    mv $dir/upload/* $dir/apps/

}

# 脚本运行参数
case "$1" in
start)
  echo "-------------- 开始启动(Service) ------------------"
  start
  echo "-------------- 启动完成(Service) ------------------"
  ;;
stop)
  echo "-------------- 开始停止(Service) ------------------"
  stop
  echo "-------------- 停止完成(Service) ------------------"
  ;;
restart)
  echo "-------------- 开始重启(Service) ------------------"
  stop
  start
  echo "-------------- 重启完成(Service) ------------------"
  ;;
update)
  echo "-------------- 开始更新(Service) ------------------"
  stop
  updateFiles
  start
  echo "-------------- 更新完成(Service) ------------------"
  ;;
*)
  printf 'Usage: %s {start|stop|restart|update}\n' "$prog"
  exit 1
  ;;
esac
```

使用

```
# restart为脚本运行参数
[root@localhost shell]# ./decode.sh restart
```