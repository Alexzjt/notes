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

### 服务熔断 break

类似保险丝

达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级方法并返回友好提示

### 服务限流 flowlimit

秒杀高并发操作，严禁一窝蜂过来拥挤，大家排队，一秒钟N个，有序进行

### @HystrixCommand

fallbackMethod = 方法名

commandProperties = {

​	@HystrixProperty ( name = "ececution.isolation.thread.timeoutInMilliseconds", value = "3000")

}

抛异常或者超过3秒都会走到备用方法名

feign.hystrix.ebabled = true