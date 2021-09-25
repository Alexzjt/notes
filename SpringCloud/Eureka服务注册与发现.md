# Eureka服务注册与发现

服务注册中心

分布式CAP理论

### 服务治理

传统的RPC远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂。

服务治理管理服务与服务之间的依赖关心，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

### 服务注册与发现

Eureka采用了CS的设计架构。Eureka Server作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用Eureka的客户端连接到Eureka Server并维持心跳连接。这样系统的维护人员就可以通过Eureka Server来监控系统值得各个微服务是否正常运行。

注册中心管理每个服务与服务之间的依赖关系。

Eureka包括Eureka Server和Eureka Client

1. Eureka Server提供服务注册服务。各个节点通过配置启动后，会在Eureka Server进行注册。Eureka Server的服务注册表将会存储所有可用服务节点的信息，服务节点的信息可用直接在界面中至关看到。
2. Eureka Client是一个Java可几乎都拿。具备内置的，使用轮询负载算法的负载均衡器。应用启动后，默认30秒发送一次心跳。Eureka Server在多个周期 （90秒 ）没有收到某个节点的心跳后，会把服务节点移除。

服务注册：将服务信息写入注册中心

服务发现：从注册中心获取服务信息 通过name取地址

### Eureka Server

引入jar **spring-cloud-starter-netflix-eureka-server**

老版本是spring-cloud-starter-eureka

要在SpringBoot启动类上面加

**@EnableEurekaServer**

### Eureka Client

引入jar **spring-cloud-starter-netflix-eureka-client**

老版本是spring-cloud-starter-eureka

**@EnableEurekaClient**

名字是spring.application.name

eureka.client.service-url.defaultZone 配Server地址

不想注册则eureka.client.register-with-eureka false

### Eureka 集群

微服务RPC远程服务调用最核心的是**高可用**

集群原理是：互相注册、相互守望

两个Server，A注册到B，B注册到A。注意服务名字不同

web页面上可以看 DS Replicas

eureka.client.service-url.defaultZone 配Server集群地址，两个之间逗号

### 负载均衡

使用restTemplate时需要@loadBalanced地址 org.springframework.cloud.client.loadbalancer

可以直配http://服务名 而不是 具体的地址

### actuator

/actuator/health   {status: up}

eureka.instance.instance-id: XXXX

eureka.instance.prefer-ip-address: true   访问路径可以显示IP地址

### 服务发现Discovery

对于注册进Eureka里面的微服务，可以通过服务发现来获取该服务的信息

**@EnableDiscoveryClient**

DiscoveryClient可以获取所有服务，所有实例等等

### Eureka自我保护

保护模式：某时刻某个微服务不可用了，Eureka不会立刻清理，依旧会保存该微服务信息

当EurekaServer节点在短时间内丢失过多客户端时，可能发生网络故障时，这个节点会进入自我保护模式

属于CAP里面的AP分支

eureka服务端 eureka.server.enable-self-preservation: false   关闭自我保护机制

eureka客户端 可以调整心跳间隔

### 与Consul、Zookeeper异同

| 组件名    | 语言 | CAP  | 服务健康检查 | 对外暴露接口 | Spring Cloud集成 |
| --------- | ---- | ---- | ------------ | ------------ | ---------------- |
| Eureka    | java | AP   | 可配支持     | HTTP         | 已集成           |
| Consul    | go   | CP   | 支持         | HTTP、DNS    | 已集成           |
| Zookeeper | java | CP   | 支持         | 客户端       | 已集成           |

### CAP

**C Consistency强一致性**

**A Availability 可用性**

**P Partition tolerance 分区容错性**

CAP理论关注力度是数据，而不是整体系统设计

**一个分布式系统不可能同时很好的满足一致性、可用性、分区容错性这三个需求**

最多只能较好的满足两个。

CA 单点集群、满足一致性、可用性的系统。可扩展性一般

CP 满足一致性、分区容错性 性能一般

AP 满足可用性、分区容错性 一致性要求低