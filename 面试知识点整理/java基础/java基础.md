# java基础

- java 跨域https://zhuanlan.zhihu.com/p/43564114

- 内部类

  - 
  
- 设计模式遵循的原则

  - 优化代码第一步：单一职责原则
  - 让程序更稳定更灵活：开闭原则
      - 软件中的对象（类、模块、函数等）应该对于扩展是开放的，对于修改是封闭的。
    - 当软件需要变化时，我们应该尽量通过扩展的方式来实现变化，而不是通过修改已有的代码来实现。
  - 构建扩展性更好的系统：里式替换原则
    - 所有引用基类的地方必须能透明地使用其子类对象。
  - 让项目拥有变化的能力：依赖倒置原则
    - 使得高层次的模块不依赖于低层次的模块的实现细节的目的
    - 高层模块不应该依赖底层模块，两者都应该依赖其抽象
    - 抽象不依赖于细节
    - 细节应该依赖抽象
  - 系统有更高的灵活性：接口隔离原则
    - 客户端不应该依赖它不需要的接口。另一种定义是：类之间的依赖关系应该建立在最小的接口上。接口隔离原则将非常庞大、臃肿的接口拆分成更小和更具体的接口，这样客户端将会只需知道他们感兴趣的方法。接口隔离原则的目的是系统解开耦合，从而容易重构、更改和重新部署。
  - 更好地扩展性：迪米特原则
    - 也称为最小知识原则。一个对象应该对其他对象有最少的了解。通俗的讲，一个类应该对自己需要耦合或调用的类知道得最小，类的内部如何实现与调用者或者依赖者没关系，调用者或者依赖者只需要知道它需要的方法即可，其他的可一概不管。类与类之间关系越密切，耦合越大，当一个类方法改变时，对另一个类的影响也越大。
  
- java中的数组互相赋值

  - 使用for循环，但是效率低

  - 使用object的clone()方法

    - `int[] b=(int[]) a.clone();//别忘了强制类型转换`

  - 使用System提供的静态方法arryCopy()

    - `public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)`

    - ex:`

      ```java
      int[] a ={1,2,3};
      int[] b = new int[3];
      System.arraycopy(a,0,b,0,3);`
      ```

    - 这个方法效率很高，而且具有一定的灵活性。许多基于数组实现的Java的集合类底层在数组复制的时候都是使用这个方法。

- java的四种引用类型
  
  - 强引用
    - 只要强引用存在，垃圾回收器将永远不会回收被引用的对象，哪怕内存不足时，JVM也会直接抛出OutOfMemoryError，不会去回收。如果想中断强引用与对象之间的联系，可以显示的将强引用赋值为null，这样一来，JVM就可以适时的回收对象了
  - 软引用
    - 在内存不足的情况下，软引用将会被自动回收
  - 弱引用
    - 无论内存是否足够，只要 JVM 开始进行垃圾回收，那些被弱引用关联的对象都会被回收。
  - 虚引用
    - 虚引用是最弱的一种引用关系，如果一个对象仅持有虚引用，那么它就和没有任何引用一样，它随时可能会被回收
  
- 进程和线程
  - 两者关系
  - 线程的几种状态
    - 新建状态
    - 就绪状态
    - 运行状态
    - 阻塞状态
      - 等待阻塞wait方法
      - 同步阻塞：在获取synchronized同步锁失败，没获得资源suspend
      - 其他阻塞：通过调用线程的sleep或者join发出io请求之后，线程进入阻塞状态，当sleep超时，join等待线程终止或者超时，或者处理io完毕，线程重新转入就绪状态
    - 死亡状态
  
- 类初始化顺序：静态->初始化->构造函数
  - 静态初始化块、静态初始化语句按照顺序从上到下进行初始化为0/null
  - 按照顺序执行赋值语句
  - 遇到new时，非静态初始化块、非静态初始化语句按照顺序（从上到下）进行初始化
  - 最后执行对应的构造函数，类加载完成
  
- java序列化https://www.runoob.com/java/java-serialization.html
  - 该机制中，一个对象可以被表示为一个字节序列，该字节序列包括该对象的数据、有关对象的类型的信息和存储在对象中数据的类型。
  - 序列化的作用：
    - 将序列化对象写入文件之后，可以从文件中读取出来，并且对它进行反序列化，也就是说，对象的类型信息、对象的数据，还有对象中的数据类型可以用来在内存中新建对象。
    - 整个过程都是 Java 虚拟机（JVM）独立的，也就是说，在一个平台上序列化的对象可以在另一个完全不同的平台上反序列化该对象。
  - 序列化的条件
    - 该类必须实现 java.io.Serializable 接口。
    - 该类的所有属性必须是可序列化的。如果有一个属性不是可序列化的，则该属性必须注明是短暂（transient）的。（检验一个类的实例是否能序列化十分简单， 只需要查看该类有没有实现 java.io.Serializable接口。）
  
- transient
  
  - 被transient修饰的变量将不再是对象持久化的一部分，该变量在对象序列化之后无法被再次访问
  - transient关键字只能修饰变量，不能修饰方法和类。
  
- String的intern方法执行原理：
  
  - intern() 方法返回字符串对象的规范化表示形式。
  - 对于任意两个字符串 s 和 t，当且仅当 s.equals(t) 为 true 时，s.intern() == t.intern() 才为 true。
  - 过程：在调用”ab”.intern()方法的时候会返回”ab”
    - 这个方法会首先检查字符串池中是否有”ab”这个字符串，如果存在则返回这个字符串的引用
    - 否则就将这个字符串添加到字符串池中，然会返回这个字符串的引用
    - 字符串相加的时候，都是静态字符串的结果会添加到字符串池，如果其中含有变量（如f中的e）则不会进入字符串池中，如果其中含有变量则不会进入字符串池中
    - 采用new 创建的字符串对象不进入字符串池
  
- java体系结构
  - java程序设计语言
  - java.class文件格式
  - java应用编程接口（API）
  - java虚拟机
  - 当我们编写并运行一个Java程序时，就同时运用了这四种技术，用**Java程序设计语言**编写源代码，把它编译成**Java.class文件格式**，然后再在**Java虚拟机中运行class文件**。当程序运行的时候，它通过调用class文件实现了**Java API的方法**来满足程序的Java API调用
  
- object类
  - 构造函数
  - hashcode()和equals
  - wait()，notify()，notifyAll()
  - toString()
  - getClass()
  - clone()
  - finalize()
  
- 为什么要重写 hashcode 和 equals 方法？
  - 因为两个对象在通过equals方法来比较是否相等时，首先会通过hashcode方法计算两个对象的哈希值是否相等，如果相等才会进一步通过equals方法进行比较，所以重写equals方法时，需要重写hashcode方法
  - 怎么重写hashCode方法
    - 第一种就是重写hashcode方法，自己写；
    - 第二种，利用lombok提供的注解功能 ，在实体类上加上@DATA和@EqualsAndHashCode两个注解
  
- stringbuffer和stringbuilder以及和string的区别
  - String
    - 除了hash属性之外，其他属性都被声明为final，是不可变的
  - Stringbuffer就是为了解决大量字符串拼接产生很多中间对象而提供的一个类
    - 提供了append和add方法
    - 它的本质是一个线程安全的可修改的字符序列，把所有修改数据的方法都加上了synchronized
    - 但是保证了线程安全是需要性能的代价的。
  - StringBuilder：在很多情况下我们的字符串拼接操作不需要线程安全
    - 它和StringBuffer本质上没什么区别，就是去掉了保证线程安全的那部分，减少了开销。
  
- 面向过程和面向对象
  - 面向过程就是分析出解决问题所需要的步骤，然后用函数把这些步骤一步一步实现，使用的时候一个一个依次调用就可以了；
  - 面向对象是把构成问题事务分解成各个对象，建立对象的目的不是为了完成一个步骤，而是为了描叙某个事物在整个解决问题的步骤中的行为。
  - 面向过程的优缺点
    - 性能比面向对象高，因为类调用时需要实例化，开销比较大，比较消耗资源，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发，性能是最重要的因素。 
    - 没有面向对象易维护、易复用、易扩展
  - 面向对象的优缺点
    - 易维护、易复用、易扩展，由于面向对象有封装、继承、多态性的特性，可以设计出低耦合的系统，使系统更加灵活、更加易于维护 
    - 性能比面向过程低
  
- java数据类型

  - 基本数据类型
    - 数值类型
      - 整数类型byte，short，int，long
      - 浮点类型float，double
      - 字符类型char
    - 布尔类型boolean
  - 引用数据类型
    - 数组
    - 类class
    - 接口interface
  
- java中的入口程序main方法为什么要用static修饰？

  类加载时无法创建对象，静态方法可以不通过对象调用，而非静态方法必须先new一个他的实例，所以在类加载时就可以通过main方法入口来运行程序。

  否则，要运行程序，需要new一个对象，而创建一个对象又要从main方法开始，这就导致了鸡生蛋，蛋生鸡的问题。