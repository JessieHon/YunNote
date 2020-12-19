# JUC

- AQS源码详细解读

  - AQS其实就是一个类，全称是AbstractQueuedSynchronizer，队列同步器
  - AQS是用来构建锁和其他同步组件的基础框架，它也是Java三大并发工具类（**CountDownLatch、CyclicBarrier、Semaphore**）的基础。**ReentrantLock**，甚至**BlockingQueue**也是基于它的实现
  - AQS的重要成员变量
    - AQS中主要维护了一个用volatile修饰的成员变量state（锁状态的表示）和一个可阻塞的等待队列
      - state是临界资源，也是锁的描述，表示有多少线程获取了锁。，通过CAS修改 state的方法
      - 除此之外，还维护了等待队列CHL的头结点和尾节点
      - CHL队列由链表实现，以自旋的方式获取资源，是可阻塞的先进先出的双向队列。通过**自旋和CAS**操作保证节点插入和移除的原子性。当有线程获取锁失败，就被添加到队列**末尾**。
  - 总结
    - 关于AQS，比较重要的就是获取资源和释放资源的方法，里面用到了大量的CAS操作和自旋。AQS里面维护了两个队列，一个是**等待队列**（CHL），还有一个是**条件队列**（condition）。
    - acquire()尝试获取资源，如果获取失败，将线程插入等待队列。插入后，并没有放弃获取资源，而是根据前置节点状态状态判断是否应该继续获取资源。如果前置节点是头结点，继续尝试获取资源；如果前置节点是SIGNAL状态，就中断当前线程，否则继续尝试获取资源。直到当前线程被阻塞或者获取到资源，结束。
      release()释放资源，需要唤醒后继节点,判断后继节点是否满足情况。如果后继节点不为空且不是作废状态,则唤醒这个后继节点；否则从尾部往前面找适合的节点，找到则唤醒。
    - 调用await()，线程会进入条件队列，等待被唤醒，唤醒后以自旋方式获取资源或处理中断异常；调用signal()，线程会插入到等待队列，唤醒被阻塞的线程。

- reentrantlock    https://zhuanlan.zhihu.com/p/65727594

  - ReentrantLock 就是一个普通的类，它是基于 AQS(AbstractQueuedSynchronizer)来实现的。

  - 是一个**重入锁**：一个线程获得了锁之后仍然可以**反复**的加锁，不会出现自己阻塞自己的情况。

  - 锁类型：可以通过构造方法指定

    - 公平锁

      ```text
       //默认非公平锁
       public ReentrantLock() {
       sync = new NonfairSync();
       }
      ```

    - 非公平锁

    ```text
     //公平锁
     public ReentrantLock(boolean fair) {
     sync = fair ? new FairSync() : new NonfairSync();
     }
    ```

    - 默认一般使用非公平锁，它的效率和吞吐量都比公平锁高很多

    - 区别：

      - 公平锁与非公平锁的差异主要在获取锁：

        公平锁就相当于买票，后来的人需要排到队尾依次买票，**不能插队**。

        而非公平锁则没有这些规则，是**抢占模式**，每来一个人不会去管队列如何，直接尝试获取锁。

- countdownLatch实现原理

  - 创建计数器：当我们调用CountDownLatch  countDownLatch=new CountDownLatch(4)  时候，此时会创建一个AQS的同步队列，并把创建CountDownLatch 传进来的计数器赋值给AQS队列的  state，所以state的值也代表CountDownLatch所剩余的计数次数；
  - 阻塞线程：当我们调用countDownLatch.wait()的时候，会创建一个节点，加入到AQS阻塞队列，并同时把当前线程挂起。
  - 计数器递减：当我们调用countDownLatch.down()方法的时候，会对计数器进行减1操作，AQS内部是通过释放锁的方式，对state进行减1操作，当state=0的时候证明计数器已经递减完毕，此时会将AQS阻塞队列里的节点线程全部唤醒。

- LinkedBlockingQueue

  - LinkedBlockingQueue是一个单向链表实现的阻塞队列。该队列按 FIFO（先进先出）排序元素，新元素插入到队列的尾部，并且队列获取操作会获得位于队列头部的元素。链接队列的吞吐量通常要高于基于数组的队列，但是在大多数并发应用程序中，其可预知的性能要低。

  - LinkedBlockingQueue还是可选容量的(防止过度膨胀)，即可以指定队列的容量。如果不指定，默认容量大小等于Integer.MAX_VALUE。

  - 说明

    - LinkedBlockingQueue继承于AbstractQueue，它本质上是一个FIFO(先进先出)的队列。
    - LinkedBlockingQueue实现了BlockingQueue接口，它支持多线程并发。当多线程竞争同一个资源时，某线程获取到该资源之后，其它线程需要阻塞等待。
    - LinkedBlockingQueue是通过单链表实现的。
    - head是链表的表头。取出数据时，都是从表头head处取出
    - last是链表的表尾。新增数据时，都是从表尾last处插入。
    - count是链表的实际大小，即当前链表中包含的节点个数。
    - capacity是列表的容量，它是在创建链表时指定的。
    - putLock是插入锁，takeLock是取出锁；notEmpty是“非空条件”，notFull是“未满条件”。通过它们对链表进行并发控制。

  - LinkedBlockingQueue在实现“多线程对竞争资源的互斥访问”时，对于“插入”和“取出(删除)”操作分别使用了不同的锁。对于插入操作，通过“插入锁putLock”进行同步；对于取出操作，通过“取出锁takeLock”进行同步。     此外，插入锁putLock和“非满条件notFull”相关联，取出锁takeLock和“非空条件notEmpty”相关联。通过notFull和notEmpty更细腻的控制锁。

    - -- 若某线程(线程A)要取出数据时，队列正好为空，则该线程会执行notEmpty.await()进行等待；当其它某个线程(线程B)向队列中插入了数据之后，会调用notEmpty.signal()唤醒“notEmpty上的等待线程”。此时，线程A会被唤醒从而得以继续运行。 此外，线程A在执行取操作前，会获取takeLock，在取操作执行完毕再释放takeLock。
    - -- 若某线程(线程H)要插入数据时，队列已满，则该线程会它执行notFull.await()进行等待；当其它某个线程(线程I)取出数据之后，会调用notFull.signal()唤醒“notFull上的等待线程”。此时，线程H就会被唤醒从而得以继续运行。 此外，线程H在执行插入操作前，会获取putLock，在插入操作执行完毕才释放putLock。

    ​	