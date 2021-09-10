# ArrayList

默认长度10，Object[]。扩容1.5倍。线程不安全

```java
List<String> stringList = new ArrayList<>();
        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                stringList.add(UUID.randomUUID().toString().substring(0,8));
                System.out.println(stringList);
            }, "thread-"+i).start();
        }
        // java.util.ConcurrentModificationException
```

1. Vector  通过synchronized解决
2. Collections.synchronizedList
3. 写时复制 CopyOnWriteArrayList

写时复制 读写分离的思想

```java
/**
 * Appends the specified element to the end of this list.
 *
 * @param e element to be appended to this list
 * @return {@code true} (as specified by {@link Collection#add})
 */
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

