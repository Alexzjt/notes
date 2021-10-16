# JVM

java8之后永久代更换为元空间

##### 新生代 Young 堆的1/3

1. Eden 8/10
2. From Survivor 1/10
3. To Survivor 1/10

##### Old 老年代 2/3 堆空间

-Xmx = -XX:MaxHeapSize  最大堆内存  初始化时为系统内存的1/4

-Xms = -XX:InitialHeapSize 堆内存初始值 物理内存1/64  一般跟-Xmx 和-Xms设为一样大

-Xss  = -XX:ThreadStackSize  设值单个线程栈的大小，一般默认为 512k到1024k  初始值为0代表使用系统默认值 64位linux默认是1M

-Xmn 设值年轻代大小

-XX:MetaspaceSize 设值元空间大小。类似永久代，都是JVM规范中方法区的实现。元空间和永久代之间最大的区别在于：元空间不在jvm中，而使用native内存。因此元空间默认仅受native内存限制

-XX:+PrintGCDetails  GC时打印相关log。young区大小变化，堆内存大小变化。FullGC时还有Old区大小变化。时间

-XX:SurvivorRatio 设置Eden区比例占多少， 两个Survivor默认相同

-XX:NewRatio 设置年轻代与老年代在堆结构中的占比

-XX:MaxTenuringThreshold 默认15 范围是0到15

### 如何查看JVM初始化参数

jps查看java进程号

jinfo -flags 5988  查看所有初始化参数

**java -XX:+PrintFlagsInitial** 直接查看初始的全局配置

**java -XX:+PrintFlagsFinal** 如果有冒号就代表人为修改过

**java -XX:+PrintCommandLineFlags**

### JVM参数类型

1. 标配参数 例如 -version等等
2. X参数
3. XX参数
   1. 布尔类型
   2. KV设值类型

### 运行时如何查看

**Runtime.getRuntime().totalMemory()**

### 强引用 弱引用 软引用 虚引用

强 Reference：内存不足开始GC时，对于强引用的对象，就算是出现了OOM也不会对该对象进行回收

软 Soft：内存充足时不回收，不足时被回收。例如Mybatis有一些

弱 Weak：每次GC都肯定被回收

虚 Phantom：和没引用差不多。每次GC都肯定被回收。必须和ReferenceQueue联合使用。主要作用是跟踪对象被垃圾回收的状态。PhantomReference的get方法总是返回null。设置虚引用的唯一目的，就是在这个对象被收集器回收的时候收到一个系统通知或者后续添加进一步的处理。Java允许使用finalize方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。

ReferenceQueue：GC之后会被放到这个队列

创建引用的时候可以指定关联的队列，当GC释放对象内存的时候，会将其加入到引用队列。当程序发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采用必要的行动。

这相当于是一种通知机制。

### OOM的异常类型

StackOverFlowError 方法死循环

OutOfMemoryError：Java Heap Space 直接new大对象，超过堆的大小

OutOfMemoryError：GC overhead limit exceeded 超过98%的时间用来GC并回收了不到2%的堆内存

OutOfMemoryError：Direct buffer memory

OutOfMemoryError：unable to create new native thread

OutOfMemoryError：Metaspace 

### 垃圾回收器

##### Serial （新老1:1)

暂停用户线程。只用一个单线程。会Stop The World

-XX:+UseSerialGC 开启后会使用Serial(Young)+Serial Old，新生代老生带都会使用串行回收。新生代使用复制算法，老年代使用标记整理算法

##### ParNew（新老N:1)

ParNew是新生代并行收集，老年代还是单线程（废弃）。一般配合老年代的CMS工作

-XX:+UseParNewGC 表示 ParNew+Serial Old（这个组合已废弃）

##### Parallel Scavenge（新老N:N) Java8默认

也是新生代垃圾收集器，复制算法

吞吐量优先收集器。高效利用CPU的时间，多用于后台运算，而不需要交互。

自适应调节。虚拟机根据当前系统的运行清理收集性能监控信息，动态调整参数以便提供最合适的停顿时间。

-XX:+UseParallelGC 或者 -XX:+UseParallelOldGC 可以互相激活

##### CMS Concurrent Mark Sweep 并发标记清除 以获取最短回收停顿时间为目标的收集器

适合BS系统、互联网、堆内存大、CPU核数多的服务器端应用。希望系统停顿时间最短。并发收集低停顿，并发指的是与用户线程一起执行。

出错后，降级为Serial Old收集器。

-XX:+UseConcMarkSweepGC 搭配的是ParNew

1. 初始标记 CMS initial mark 会STW。标记GC Root可以**直达**的对象，耗时短
2. 并发标记 CMS concurrent mark。从第一步标记的对象出发，并发的标记可达对象
3. 重新标记 CMS remark 会STW。重新修正第二部期间变化以及新对象，耗时短
4. 并发清除 CMS concurrent sweep。

缺点：

1. CPU压力大
2. 标记清除内存碎片多

##### G1 

-XX:+UseG1GC

比CMS的优点：

1. 不会产生很多内存碎片
2. STW更可控。G1在停顿时间基础上添加了预测机制，用户可以指定期望停顿时间 -XX:MaxGCPauseMillis

改变Eden、Survivor、Tenured等内存区域不再连续，变成一个个大小一样的Region。化整为零。避免全内存扫描。每个Region大小是1MB-32MB，最多2048个region，能支持的最大内存为64G。G1**不要求对象的存储一定是物理上连续的，只要逻辑上连续。**

-XX:G1HeapRegionSize=n 可以指定分区大小 1MB-32MB， 2的幂

Region的角色是会变的，可能这次属于Eden，下次属于Survivor。

1. 对于新生代，会STW，将活的对象拷贝到老年代或者Survivor
2. 老年代，会将对象从一个Region到另一个Region
3. Humongous巨大区。如果一个对象占用空间大于Region的50%以上。G1会认为这是一个巨型对象，会直接分配到老年代。如果一个巨大区装不下，则会寻找连续的巨大区存储，可能会启动FullGC