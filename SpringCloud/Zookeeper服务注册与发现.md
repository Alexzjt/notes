# Zookeeper

因为eureka停更了，这个是Eureka的替代方案

Zookeeper是一个分布式协调工具。可以实现注册中心功能

关闭Linux服务器防火墙后启动Zookeeper服务器

Zookeeper服务器取代Eureka服务器，zk作为服务注册中心

引入jar **spring-cloud-starter-zookeeper-discovery**

确保客户端的zk版本和服务端的zk版本一致

spring.cloud.zookeeper.connect-string: zk服务端的ip端口

主类上增加**@EnableDiscoveryClient**

**服务节点是临时节点**

