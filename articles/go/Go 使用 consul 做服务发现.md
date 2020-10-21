# 系列文章目录

[微服务 | Spring Cloud(一)：从单体SSM 到 Spring Cloud](https://blog.csdn.net/zyndev/article/details/84102893)  
[Go | Gin 解决跨域问题跨域配置
](https://blog.csdn.net/zyndev/article/details/108188255)

---

@[TOC](文章目录)

---

# 前言
前面一章讲了微服务的一些优点和缺点，那如何做到


# 一、目标

# 二、使用步骤
## 1. 安装 consul

我们可以直接使用官方提供的二进制文件来进行安装部署，其官网地址为 https://www.consul.io/downloads
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201012225904133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5bmRldg==,size_16,color_FFFFFF,t_70#pic_center)
下载后为可执行文件，在我们开发试验过程中，可以直接使用 `consul agent -dev` 命令来启动一个单节点的 consul

在启动的打印日志中可以看到 `agent: Started HTTP server on 127.0.0.1:8500 (tcp)`, 我们可以在浏览器直接访问 `127.0.0.1:8500` 即可看到如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201012231011560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p5bmRldg==,size_16,color_FFFFFF,t_70#pic_center)
这里我们的 consul 就启动成功了

## 2. 服务注册
在网络编程中，一般会提供项目的 IP、PORT、PROTOCOL，在服务治理中，我们还需要知道对应的服务名、实例名以及一些自定义的扩展信息

```go
type ServiceInstance interface {

	// return The unique instance ID as registered.
	GetInstanceId() string

	// return The service ID as registered.
	GetServiceId() string

	// return The hostname of the registered service instance.
	GetHost() string

	// return The port of the registered service instance.
	GetPort() int

	// return Whether the port of the registered service instance uses HTTPS.
	IsSecure() bool

	// return The key / value pair metadata associated with the service instance.
	GetMetadata() map[string]string
}
```


## 3. 服务发现


# 总结
通过使用 consul api 我们可以简单的实现基于 consul 的服务发现，在通过结合 http rpc 就可简单的实现服务的调用，下面一章来简单讲下 go 如何发起 http 请求，为我们做 rpc 做个铺垫 

# 参考
- https://www.consul.io/api-docs
- https://github.com/hashicorp/consul/tree/master/api
