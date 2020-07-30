## 简介
如果大家理解了obj.wait和obj.notify方法的话，那么就很容易理解Condition对象了。它和wait和notify方法的作用是大致相同的。但是wait和notify方法是和synchronized关键字合作使用的，而Condition是与重入锁相关联的。通过Condition的newCondition()方法可以生成一个与当前重入锁绑定的Condition实例。利用Condition对象，我们就可以让线程在合适的时间等待，或者在某一个特定的时间得到通知，继续执行。

## 参考资料
https://blog.csdn.net/a910626/article/details/51900941