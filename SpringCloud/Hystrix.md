# Hystrix

服务降级、断路器、服务熔断、服务隔离

Hystrix是用于处理分布式系统的延迟和容错的开源库。在分布式系统中，许多依赖会不可避免的调用失败，比如超时、异常。Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

断路器本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控，类似熔断保险丝，向调用方返回一个符合预期的、可处理的备选响应FallBack。而不是长时间等待或者抛异常。这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延。

1. 服务降级
2. 服务熔断
3. 接近实时的监控

**spring-cloud-starter-netflix-hystrix**

消费端、服务端都可以用，但是一般用在消费端

主启动类 @EnableCircuitBreaker

### 服务降级 fallback

服务器忙，请稍后再试，不让客户端等待并立即返回一个友好的提示

那些情况会降级？

1. 程序运行异常
2. 超时
3. 服务熔断触发服务降级
4. 线程池、信号量打满也会导致服务降级

##### @HystrixCommand

fallbackMethod = 方法名

commandProperties = {

​	@HystrixProperty ( name = "ececution.isolation.thread.timeoutInMilliseconds", value = "3000")

}

抛异常或者超过3秒都会走到备用方法名

feign.hystrix.ebabled = true

##### 避免代码膨胀

在类上直接配

@DefaultProperties(defaultFallback = "")

除了个别重要核心业务有专属fallback方法，其他普通的可以通过此注解，走统一处理逻辑

方法上仍然要配@HystrixCommand

##### 为FeignClient实现服务降级

@FeignClient (fallback = FeignClient接口的实现类)

实现类实现降级逻辑

在服务端提供方已经down的情况下，做服务降级

### 服务熔断 break

类似保险丝

达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级方法并返回友好提示

熔断机制是应对雪崩效应的一种微服务链路保护机制，当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。

**当检测到该节点微服务调用响应正常后，恢复调用链路。**

SpringCloud中，熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况。

当失败的调用到一定阈值时，默认情况是5秒内20次失败，就会启动熔断机制。熔断机制的注解也是@HystrixCommand

https://martinfowler.com/bliki/CircuitBreaker.html

### 服务限流 flowlimit

秒杀高并发操作，严禁一窝蜂过来拥挤，大家排队，一秒钟N个，有序进行

```java
@HystrixCommand(fallbackMethod = "", commandProperties = {
        @HystrixProperty(name = "circuitBreaker.enabled", value = "true"), //是否开启断路器
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "20"), //请求次数
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"), // 失败率
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "5000"), //时间窗口

})
```

现象：大量失败请求，达到条件触发断路器后，正确的请求也会被熔断，返回fallback的内容。直到时间窗口期的请求低于断路器的阈值

1. 熔断打开：请求不再调用当前服务，内部设置时钟一般为MTTR，当打开时长达到所设时钟则进入半熔断状态
2. 熔断关闭：熔断关闭不会对服务进行熔断
3. 部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断