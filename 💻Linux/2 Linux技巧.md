# Linux技巧

### 1 防火墙

```
systemctl start firewalld  # 开启防火墙
systemctl stop firewalld   # 关闭防火墙
systemctl status firewalld # 查看防火墙开启状态，显示running则是正在运行
firewall-cmd --reload      # 重启防火墙，永久打开端口需要reload一下

# 添加开启端口，--permanent表示永久打开，不加是临时打开重启之后失效
firewall-cmd --permanent --zone=public --add-port=8888/tcp

# 查看防火墙，添加的端口也可以看到
firewall-cmd --list-all
```

切换root

```
sudo su
```



### 2 查看最繁忙（占用CPU高耗时）的线程

- 获取项目的pid，使用`jps`或者`ps -ef | grep java`
- `top -Hp pid`，打出来的LWP（LWP表示轻量级进程，也就是操作系统原生线程的线程号：light weight process)）是十进制的，`jps pid`打出来的本地线程号是十六进制的
- jstack pid 【**注意：jstack 进程pid，若是jstack 子线程pid，需要转换为十六进制**】

```
processid=$(top -bn 1 | grep java | head -n1 | awk '{ print $1}');
threadid=$(top -Hbn 1 -p ${processid} | grep java | head -n1 | awk '{ print $1}' | xargs printf  '%x');
sudo /usr/local/src/jdk1.8.0_152/bin/jstack $processid | grep "0x$threadid" -A10 --color;
```



### 3 系统配置

```
/proc/sys
```

**可以打开的文件句柄数是多少**

```
–$ cat /proc/sys/fs/file-max
```