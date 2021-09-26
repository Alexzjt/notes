# OpenFeign

Feign是一个声明式的WebService客户端。使用Feign能让编写WebService客户端更加简单。

使用方法是定义一个服务接口然后在上面加注解。Feign也支持可拔插式的编码器和解码器。SpringCloud对Feign进行了封装。使其支持了SpringMVC标准注解和HTTPMessageConverters。

Feign可以与Eureka和Ribbon组合使用以支持负载均衡。

**Feign集成了Ribbon**

**spring-cloud-starter-openfeign**

主启动类 **@EnableFeignClients**

接口上面加@FeignClient

value = 微服务名

### 超时控制

OpenFeign默认等待1秒，超时报错

ribbon.ReadTimeout 

ribbon.ConnectTimeout

### 日志打印

```java
@Configuration
public class FeignConfig {
    @Bean
    public Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

logging.level.  包名 debug

feign日志以什么级别监控哪个接口