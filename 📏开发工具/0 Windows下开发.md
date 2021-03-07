# Windows下搭建Linux虚拟容器开发环境

## 安装虚拟机

略



## 使用X shell连接虚拟机

第一步：首先确保有网络，如果有图形界面可上网试一下。如果没有图形界面，可以用ping命令，例如：`ping bing.com`。在这里网络我选择的是**Bridged模式**。

第二步：使用`sudo apt-get update`更新依赖包，接着按照提示，安装`net-tools`，命令：`sudo apt install net-tools`

第三步：`ifconfig`



**Connection failed.**

第四步：相互ping一下，可以发现虚拟机无法ping通本机

第五步：虚拟机安装ssh，命令：`sudo apt install ssh`



**root无法连接**

```
sudo apt -y install vim
sudo vim /etc/ssh/sshd_config

/PermitRootLogin 
# 按照i，增加一行，填写 PermitRootLogin yes
按ESC 输入: wq!

重启ssh
service ssh restart

修改root密码
sudo passwd root
```



## Docker

### 安装Docker

`sudo apt install docker.io`



### 安装常用软件

```bash
#首次启动
$ docker run --name mysql -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=admin mysql:5.7
$ docker run --name redis -d -p 6379:6379 redis redis-server --appendonly yes
$ docker run --name mongo -d -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=admin mongo
$ docker run --name nginx -d -p 8080:80 nginx:1.19.7

# 后续启动
$ docker start mysql
$ docker start redis
$ docker start mongo
$ docker start nginx

# 虚拟机安装redis客户端
$ sudo apt install redis-tools
```



