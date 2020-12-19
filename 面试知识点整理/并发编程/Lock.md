# Lock锁

- https://www.cnblogs.com/wuhan729/p/8601108.html

- 锁出现的背景
  - 自动释放锁导致的问题
    - 获取锁的线程释放锁只会有两种情况
      - 获取锁的线程执行完了该代码块，然后线程释放对锁的占有；
      - 线程执行发生异常，此时JVM会让线程自动释放锁。
    - 那么如果这个获取锁的线程由于要等待IO或者其他原因（比如调用sleep方法）被阻塞了，但是又没有释放锁，其他线程便只能干巴巴地等待，试想一下，这多么影响程序执行效率。
  - 线程同步带来的问题
    - 再举个例子：当有多个线程读写文件时，读操作和写操作会发生冲突现象，写操作和写操作会发生冲突现象，但是读操作和读操作不会发生冲突现象。
    - 但是采用synchronized关键字来实现同步的话，就会导致一个问题：如果多个线程都只是进行读操作，所以当一个线程在进行读操作时，其他线程只能等待无法进行读操作。
  - synchronized无法知道线程有没有获取到锁，lock可以办到

**Lock接口**

- lock的几个方法
  - lock
    - 就是用来获取锁。如果锁已被其他线程获取，则进行等待
  - tryLock
    - tryLock()方法是有返回值的，它表示用来尝试获取锁，如果获取成功，则返回true，如果获取失败（即锁已被其他线程获取），则返回false，也就说这个方法无论如何都会立即返回。在拿不到锁时不会一直在那等待。
  - tryLock（time timeUnit）
    - tryLock(long time, TimeUnit  unit)方法和tryLock()方法是类似的，只不过区别在于这个方法在拿不到锁时会等待一定的时间，在时间期限之内如果还拿不到锁，就返回false。如果如果一开始拿到锁或者在等待期间内拿到了锁，则返回true。
  - lockInterruptibly
    - lockInterruptibly()方法比较特殊，当通过这个方法去获取锁时，如果线程正在等待获取锁，则这个线程能够响应中断，即中断线程的等待状态。也就使说，当两个线程同时通过lock.lockInterruptibly()想获取某个锁时，假若此时线程A获取到了锁，而线程B只有在等待，那么对线程B调用threadB.interrupt()方法能够中断线程B的等待过程。
    - 由于lockInterruptibly()的声明中抛出了异常，所以lock.lockInterruptibly()必须放在try块中或者在调用lockInterruptibly()的方法外声明抛出InterruptedException。
- ReentrantLock是唯一实现了Lock接口的类，并且ReentrantLock提供了更多的方法。

**ReadWriteLock接口**

- 两个方法
  - Lock readLock();
  - Lock writeLock();
- ReentrantReadWriteLock实现了ReadWriteLock接口。

- 总结来说，Lock和synchronized有以下几点不同：
  - Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
  - synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
  - Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
  - 通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
  - Lock可以提高多个线程进行读操作的效率。
  - **在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。**
- 