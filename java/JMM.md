## 简介
JMM 简称Java内存模型
在多线程环境下，线程之间的要通信,就不得不提JMM(java内存模型),在JVM内部使用的java内存模型(JMM)将线程堆栈和堆之间的内存分开。


- 重排序
- 顺序一致性
- Happens-Before
- As-if-Serial

## as-if-serial语义
as-if-serial语义的意思指：不管怎么重排序（编译器和处理器为了提高并行度），（单线程）程序的执行结果不能被改变。编译器，runtime 和处理器都必须遵守as-if-serial语义。