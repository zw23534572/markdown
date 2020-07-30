# CAS
CAS(Compare And Swap)，即比较并交换。是解决多线程并行情况下使用锁造成性能损耗的一种机制，CAS操作包含三个操作数——内存位置（V）、预期原值（A）和新值(B)。

如果内存位置的值与预期原值相匹配，那么处理器会自动将该位置值更新为新值。否则，处理器不做任何操作。无论哪种情况，它都会在CAS指令之前返回该位置的值。CAS有效地说明了“我认为位置V应该包含值A；如果包含该值，则将B放到这个位置；否则，不要更改该位置，只告诉我这个位置现在的值即可。

在JAVA中，sun.misc.Unsafe 类提供了硬件级别的原子操作来实现这个CAS。 java.util.concurrent 包下的大量类都使用了这个 Unsafe.java 类的CAS操作。至于 Unsafe.java 的具体实现这里就不讨论了。

# AQS
QS(AbstractQueuedSynchronizer)，AQS是JDK下提供的一套用于实现基于FIFO等待队列的阻塞锁和相关的同步器的一个同步框架。这个抽象类被设计为作为一些可用原子int值来表示状态的同步器的基类。

如果你有看过类似 CountDownLatch 类的源码实现，会发现其内部有一个继承了 AbstractQueuedSynchronizer 的内部类 Sync 。可见 CountDownLatch 是基于AQS框架来实现的一个同步器.类似的同步器在JUC下还有不少。(eg. Semaphore )

## 参考资料
- https://blog.csdn.net/fz13768884254/article/details/82862437
- https://www.cnblogs.com/waterystone/p/4920797.html 
- https://blog.csdn.net/u010862794/article/details/72892300 ★