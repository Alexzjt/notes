# Spring AOP

### AOP常用注解

1. @Before 前置通知：目标方法之前执行
2. @After 后置通知：目标方法之后执行（始终执行）
3. @AfterReturning 返回后通知：执行方法结束前执行（异常不执行）
4. @AfterThrowing 异常通知：出现异常时候执行
5. @Around 环绕通知：环绕目标方法执行

### 面试题

##### AOP的全部通知顺序？SpringBoot或SpringBoot2对AOP的执行顺序影响？

Spring4 boot1

1. 正常情况：@Around @Before  方法执行 @Around @After @AfterReturning
2. 异常情况：@Around @Before  @After @AfterThrowing 

Spring5 boot2

1. 正常情况：@Around @Before  方法执行 @AfterReturning @After @Around
2. 异常情况：@Around @Before @AfterThrowing  @After 

##### 说说你使用AOP中碰到的坑？

### 业务类

### 新建一个切面类MyAspect并为切面类新增两个注解

### 