
## 锁的释放-获取建立的 happens before 关系
锁是 java 并发编程中最重要的同步机制。

锁除了让临界区互斥执行外，还可以让释放锁的线程向获取同一个锁的线程发送消息。下面是锁释放-获取的示例代码：
```shell
class MonitorExample {
    int a = 0;

    public synchronized void writer() {  //1
        a++;                             //2
    }                                    //3

    public synchronized void reader() {  //4
        int i = a;                       //5
        ……
    }                                    //6
}
```

假设线程 A 执行 writer() 方法，随后线程 B 执行 reader() 方法。根据 happens before 规则，这个过程包含的 happens before 关系可以分为两类：

1. 根据程序次序规则，1 happens before 2, 2 happens before 3; 4 happens before 5, 5 happens before 6。
2. 根据监视器锁规则，3 happens before 4。
3. 根据 happens before 的传递性，2 happens before 5。

上述 happens before 关系的图形化表现形式如下：
![](http://wiki.jikexueyuan.com/project/java-memory-model/images/24.png)

在上图中，每一个箭头链接的两个节点，代表了一个 happens before 关系。黑色箭头表示程序顺序规则；橙色箭头表示监视器锁规则；蓝色箭头表示组合这些规则后提供的 happens before保证。

上图表示在线程A释放了锁之后，随后线程B获取同一个锁。在上图中，2 happens before 5。因此，线程A在释放锁之前所有可见的共享变量，在线程B获取同一个锁之后，将立刻变得对B线程可见。

## 锁释放和获取的内存语义
当线程释放锁时，JMM 会把该线程对应的本地内存中的共享变量刷新到主内存中。以上面的MonitorExample 程序为例，A线程释放锁后，共享数据的状态示意图如下：

![](http://wiki.jikexueyuan.com/project/java-memory-model/images/25.png)

当线程获取锁时，JMM 会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须要从主内存中去读取共享变量。下面是锁获取的状态示意图：

![](http://wiki.jikexueyuan.com/project/java-memory-model/images/26.png)

对比锁释放-获取的内存语义与 volatile 写-读的内存语义，可以看出：锁释放与 volatile 写有相同的内存语义；锁获取与 volatile 读有相同的内存语义。

下面对锁释放和锁获取的内存语义做个总结：

- 线程 A 释放一个锁，实质上是线程 A 向接下来将要获取这个锁的某个线程发出了（线程 A 对共享变量所做修改的）消息。
- 线程 B 获取一个锁，实质上是线程 B 接收了之前某个线程发出的（在释放这个锁之前对共享变量所做修改的）消息。
- 线程 A 释放锁，随后线程 B 获取这个锁，这个过程实质上是线程 A 通过主内存向线程 B 发送消息。

## 锁内存语义的实现

本文将借助 ReentrantLock 的源代码，来分析锁内存语义的具体实现机制。

请看下面的示例代码：
```shell
class ReentrantLockExample {
int a = 0;
ReentrantLock lock = new ReentrantLock();

public void writer() {
    lock.lock();         //获取锁
    try {
        a++;
    } finally {
        lock.unlock();  //释放锁
    }
}

public void reader () {
    lock.lock();        //获取锁
    try {
        int i = a;
        ……
    } finally {
        lock.unlock();  //释放锁
    }
}
}  
```
在 ReentrantLock 中，调用 lock() 方法获取锁；调用 unlock() 方法释放锁。

ReentrantLock 的实现依赖于 java 同步器框架 AbstractQueuedSynchronizer（本文简称之为AQS）。AQS 使用一个整型的 volatile 变量（命名为 state）来维护同步状态，马上我们会看到，这个 volatile 变量是 ReentrantLock 内存语义实现的关键。 下面是ReentrantLock 的类图（仅画出与本文相关的部分）：

![](http://wiki.jikexueyuan.com/project/java-memory-model/images/27.png)