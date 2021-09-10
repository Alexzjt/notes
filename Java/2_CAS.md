# CAS是什么

比较并交换 Compare And Swap 是CPU并发原语之一。判断内存某个位置的值是否为预期值，如果是则更改为新的值，这个过程是原子的。

比较当前工作内存中的值和主内存中的值，如果相同则只需规定操作。否则继续比较直到主内存和工作内存中的值一致为止。

CAS并发原语体现在java语言中就是sun.misc.unsafe类的各个方法。调用这些方法时，JVM会实现出CAC汇编指令。这是一种完全依赖于硬件的功能，通过它实现了原子操作。由于CAS是一种系统原语，原语属于操作系统用语范畴，是由若干条指令组成的，用于完成某个功能的一个过程。**原语的执行必须是连续的，在执行过程中不允许被中断，也就是说CAS是一条CPU的原子指令，不会造成数据不一致问题。**

AtomicInteger.compareAndSet(expect, update) 

与预期值比较，相同则更新，成功返回true



##### 底层用unsafe实现

unsafe是CAS核心类，由于JAVA方法无法直接访问底层系统，需要通过本地native方法来访问。unsafe可以直接操作特定内存的数据。存在于sun.misc包中，其native可以像C的指针一样直接操作内存。

unsafe类的所有方法都是native修饰的，都是直接调用操作系统底层资源执行相应任务。

```java
/**
 * 这是AtomicInteger类的方法
 * Atomically increments by one the current value.
 *
 * @return the previous value
 */
public final int getAndIncrement() {
    return unsafe.getAndAddInt(this, valueOffset, 1);
}
```

```java
/**
 * 这是unsafe类的方法
 */
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

valueOffset表示变量值在内存中的偏移地址，unsafe通过内存偏移地址获取数据

##### CAS的缺点

1. 自旋锁，循环时间长，开销很大
2. 只能在一个共享变量的原子操作
3. ABA问题