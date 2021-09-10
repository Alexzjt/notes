# CountDownLatch

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        int count = 10;
        CountDownLatch latch = new CountDownLatch(count);
        for (int i = 0; i < count; i++) {
            int finalI = i;
            new Thread(() -> {
                System.out.println(finalI +"同学离开教室");
                latch.countDown();
            }, "Thread"+i).start();
        }
        latch.await();
        System.out.println(Thread.currentThread().getName()+"班长锁门");
    }
}
```

# CyclicBarrier

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(10, () -> {
            System.out.println("*******开始开会*****");
        });
        for (int i = 1; i <= 10; i++) {
            int finalI = i;
            new Thread(() -> {
                System.out.println("第"+ finalI +"个人来了");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }, "thread"+i).start();
        }
    }
}
```

# Semaphore

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(5);
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "\t 开始吃饭");
                    try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
                    System.out.println(Thread.currentThread().getName() + "\t 吃完走人******");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            }, "thread"+i).start();
        }
    }
}
```

