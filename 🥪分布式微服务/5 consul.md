# 5 consul

### 1 获取配置

如果没有对应环境的consul权限，可以使用

*`curl -s http://10.104.6.37:8500/v1/kv/config/xxxxr/properties | python -m json.tool`*

这种命令去查看配置，查出来的是base64  需要解码(https://base64.supfree.net/)。



### 2 consul删除服务

方式1：**PUT**请求：

```
[http://server_ip:8500/v1/agent/service/deregister/gateway-service-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003)
```



方式2：直接服务器请求：

```
curl -X PUT [http://server_ip:8500/v1/agent/service/deregister/gateway-service-9003]

// gateway-service-9003 为服务名
// server_ip:8500 为consul的ip和端口


```

https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003

 

