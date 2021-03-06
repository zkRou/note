---
title: ThreadLocal详解
author: Kairou Zeng
date: 2018/04/15
---
### ThreadLocal详解

> ThreadLocal位于java.lang.ThreadLocal.

ThreadLocal存放的值是线程内共享的，线程间互斥的，主要用于线程内共享一些数据，避免通过参数来传递。为了解决多线程中相同变量的访问冲突问题。

（与线程同步机制相比：对于多线程资源共享的问题，同步机制采用了“以时间换空间”的方式，而ThreadLocal采用了“以空间换时间”的方式。前者仅提供一份变量，让不同的线程排队访问，而后者为每一个线程都提供了一份变量，因此可以同时访问而互不影响。）

当使用ThreadLocal维护变量时，ThreadLocal为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立改变自己的副本，而不会影响其它线程所对应的副本。

从线程的角度看，目标变量就像是线程的本地变量，这也是类名“Local”所要表达的意思。

- void set(T value)

  设置当前线程的线程局部变量的值。

- T get()

  返回当前线程所对应的线程的局部变量

- void remove()

  将当前线程局部变量的值删除，目的是为了减少内存的占用。需要指出的是，当线程结束后，对应该线程的局部变量将自动被垃圾回收，所以显式调用该方法清除线程的局部变量并不是必须的操作，但它可以加快内存回收的速度。

- T initialValue()

  返回该线程局部变量的初始值，改方法是一个protected的方法，为了让子类覆盖而设计的。这个方法是一个延迟调用方法，在线程第1次调用get()或set(Object)时才执行，并且仅执行1次。ThreadLocal中的缺省实现直接返回一个null。

  ​

##### ThreadLocal是如何做到为每一个线程维护变量的副本的呢？

其实实现的思路很简单：在ThreadLocal类中有一个Map，用于存储每一个线程的变量副本，Map中元素的键为线程对象，而值对应线程的变量副本.



`相关资料`：

[[彻底理解ThreadLocal](http://blog.csdn.net/lufeng20/article/details/24314381)](http://blog.csdn.net/lufeng20/article/details/24314381/)