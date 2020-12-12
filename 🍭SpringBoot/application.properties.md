# application.properties

## boostrap与application

- boostrap.yml 系统级别，优先加载，常用于服务配置管理，例如consul、spring cloud config
- application.yml spring级别

>  The order of configuration is: command line parameters > System Env > System Properties > arthas.properties.
>
> https://arthas.aliyun.com/doc/en/arthas-properties.html



## 常用配置

```properties
#服务端口
server.port= 8080
#tomcat启动的最大线程数
server.tomcat.max-connections=1000 
#tomcat最大链接数
server.tomcat.max-threads=500 
#tomcat连接超时设置
server.connection-timeout=30000s 
spring.http.encoding.force=true
spring.http.encoding.charset=UTF-8
spring.http.encoding.enabled=true
server.tomcat.uri-encoding=UTF-8

# 配置MySQL数据源
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/kaguya?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8&useSSL=false&autoReconnect=true&rewriteBatchedStatements=TRUE&useAffectedRows=true&allowMultiQueries=true
spring.datasource.username=root
spring.datasource.password=kaguya
# 初始化大小，最小(核心线程)，最大（最大线程数）
spring.datasource.druid.initialSize=10
spring.datasource.druid.minIdle=10
spring.datasource.druid.maxActive=40
# 配置获取连接等待超时的时间
spring.datasource.druid.maxWait=60000
# 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
spring.datasource.druid.timeBetweenEvictionRunsMillis=60000
# 配置一个连接在池中最小生存的时间，单位是毫秒
spring.datasource.druid.minEvictableIdleTimeMillis=300000
spring.datasource.druid.validationQuery=show status like '%Service_Status%'
# spring.datasource.druid.validationQuery=SELECT 1
spring.datasource.druid.testWhileIdle=true
spring.datasource.druid.testOnBorrow=false
spring.datasource.druid.testOnReturn=false
# 打开PSCache，并且指定每个连接上PSCache的大小
spring.datasource.druid.poolPreparedStatements=true
spring.datasource.druid.maxPoolPreparedStatementPerConnectionSize=35

#cas
#cas服务器地址
cas.server.url= 
#登录的过滤器
cas.server.loginAction.url=/ 
#客户端服务器地址
cas.client.url= 
cas.client.sys.type= 
#首页
cas.client.index.url= 
shiro.autho.permission.error.url=/ 

# thymeleaf
#是否开启缓存
spring.thymeleaf.cache=false 
#检查模板是否存在
spring.thymeleaf.check-template-location=true 
#内容类型
spring.thymeleaf.content-type=text/html 
#启动MVC Thymeleaf视图分辨率
spring.thymeleaf.enabled=true 
#编码
spring.thymeleaf.encoding=UTF-8  
#模板编码
spring.thymeleaf.mode=HTML5 
#url后缀类型
spring.thymeleaf.suffix=.html 

# redis
#Redis数据库索引（默认为0
spring.redis.database=0 
#host
spring.redis.host=127.0.0.1
#端口
spring.redis.port= 6379
# redis客户端：jedis
#最大空闲连接数
spring.redis.jedis.pool.max-idle=30 
#最小空闲连接数
spring.redis.jedis.pool.min-idle=10
#最大连接数据库连接数
spring.redis.jedis.pool.max-active=80
 #最大等待毫秒数
spring.redis.jedis.pool.max-wait=-1s

# redis(lettuce)
# SpringBoot2.x已经将jedis改成了lettuce，见pom中的spring-boot-starter-data-redis
#最大空闲连接数
spring.redis.lettuce.pool.max-idle=30
#最小空闲连接数
spring.redis.lettuce.pool.min-idle=10
#最大连接数据库连接数
spring.redis.lettuce.pool.max-active=80
#最大等待毫秒数
spring.redis.lettuce.pool.max-wait=-1s 
spring.session.store-type=redis


# redis集群（jedis）
spring.redis.cluster.max-redirects=6
spring.redis.cluster.nodes=127.0.0.1:6677
spring.redis.password=xxxx
spring.redis.jedis.pool.max-active=80
spring.redis.jedis.pool.max-idle=30
spring.redis.jedis.pool.max-wait=2000s
spring.redis.jedis.pool.min-idle=10
#session集群使用redis
spring.session.store-type=redis


#redis哨兵
spring.redis.sentinel.master=mymaster
spring.redis.sentinel.nodes=127.0.0.1:8000,127.0.0.2:8000,127.0.0.3:8000
spring.redis.jedis.pool.max-active=80
spring.redis.jedis.pool.max-idle=30
spring.redis.jedis.pool.max-wait=2000s
spring.redis.jedis.pool.min-idle=10


#mybatis
mybatis.mapper-locations=classpath:META-INF/mybatis/**/*Mapper.xml
#开启驼峰
mybatis.configuration.map-underscore-to-camel-case=true
mapper.mappers=com.example.test.dao.BaseMapper
mapper.not-empty=false
mapper.identity=MYSQL
#pagehelper
pagehelper.helper-dialect=mysql
pagehelper.reasonable=true
#
pagehelper.support-methods-arguments=true
pagehelper.params=count=countSql


#rabbitmq
#支持多个
spring.rabbitmq.addresses=127.0.0.1:5672,128.0.0.1:5672 
spring.rabbitmq.virtual-host=myhost
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
#指定心跳超时，0为不指定.
spring.rabbitmq.requested-heartbeat=20s 
#消息手动确认
spring.rabbitmq.listener.simple.acknowledge-mode=manual 
#每次从队列中取几个
spring.rabbitmq.listener.simple.prefetch=5 
#消费者的数量 出队
spring.rabbitmq.listener.simple.concurrency=10 
 #缓存中保持的Channel数量
spring.rabbitmq.cache.channel.size=100
# org.springframework.amqp.rabbit.config.SimpleRabbitListenerContainerFactory#setConcurrentConsumers
# 指定创建时当前消费者数量（the minimum number of consumers to create）
```