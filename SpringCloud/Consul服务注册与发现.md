# Consul服务注册与发现

也是Eureka的替代方案。

Consul是一套开源的分布式服务发现和配置管理系统，用Go语言编写

提供了服务治理、配置中心、控制总线。一种完整的服务网格解决方案。

基于raft协议、简洁。支持健康检查、支持HTTP和DNS协议。支持跨数据中心的WAN集群。

提供图形界面。

引入jar **spring-cloud-starter-consul-discovery**

spring.cloud.consul:

​	host、port、discovery.service-name