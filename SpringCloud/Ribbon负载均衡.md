# Ribbon

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端。负载均衡的工具。

主要功能是提供客户端的软件负载均衡算法和服务调用。

### Load Balance

将用户的请求平摊到多个服务上。从而达到系统的HA。

常见的负载均衡有Nginx LVS 硬件F5等等

### Ribbon本地负载均衡客户端 VS Nginx服务端负载均衡

Nginx是服务器负载均衡，客户端的所有请求都会交给nginx，然后由nginx实现转发请求。即负载均衡是由服务端实现的。**集中式的LB**。服务的调用方和提供方之间使用独立的LB设施，由该设施转发。

Ribbon本地负载均衡，在调用微服务接口时，会在注册中心上获取注册信息服务列表之后缓存到JVM本地，从而在本地实现远程调用。**进程内LB**。将LB逻辑集成到消费方，消费方通过服务注册中心获知有哪些地址可用，然后自行选择一个合适的服务器。

Ribbon只是一个类库，集成在消费方进程。消费方通过它来获取到服务提供方的地址。

**负责均衡+RestTemplate调用**

1. 先选择EurekaServer，优先选择负载少的Server
2. 根据用户指定策略，从server取得服务注册列表中选择一个地址

Ribbon提供轮询、随机、根据响应时长加权

**spring-cloud-starter-netflix-ribbon**

spring-cloud-starter-netflix-eureka-client自带了spring-cloud-starter-netflix-ribbon

### IRule 规则接口

抽象类AbstractLoadBalanceRule

实现类

1. RoundRobinRule 轮询
2. RandomRule 随机选择
3. RetryRule 先轮询，如果获取服务失败则会在指定时间内重试
4. BestAvailableRule 过滤断路的服务，然后选择并发量最小的服务
5. AvailabilityFilteringRule 先过滤掉故障实例，再选择并发较小的实例
6. WeightedResponseTimeRule extends RoundRobinRule 加权，响应越快的实例权重越大

##### 如何替换

注意不能和SpringBoot启动类放到一个包下，不能被ComponentScan扫描，否则会被所有的Ribbon客户端共享

启动类增加@RibbonClient(name = "", configuration=MySelfRule.class)

### 负载均衡算法原理

rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标，每次服务重启动后rest接口计数从1开始