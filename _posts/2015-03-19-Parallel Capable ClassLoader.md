---
title: Parallel Capable ClassLoadder
layout: post
---

{:toc}

JDK 7 对Class Loader 进行了更新，加入了Parallel Capable(并行能力)的加载机制，默认是启用的。

## 什么是Parallel Capable

当在多线程执行环境时，不同的线程load不同的类时，不会阻塞，即支持并发加载。

## Class Loader In JDK 6

在JDK 6中，ClassLoader的loadClass是同步方法。此时锁的使用粒度太粗了，导致了在多线程环境下加载类时性能比较差，而且易发生死锁问题。

![ClassLoaderJdk6](/public/img/ParallelCapableClassLoader/ClassLoaderJdk6.png)


### ClassNotFoundException

在多线程情况下，当大量的ClassNotFoundException发生时，就要小心了。意味着在load处可能会发生不少wait for lock的情况。会拖慢程序处理的吞吐量。应该予以重视。

```java

public class ClassloadingLockMock {

  static AtomicLong index = new AtomicLong(0);
  
  static class LoaderWorker extends Thread {
    @Override
    public void run() {
      while (true) {
        try {
          String className = "java.lang.ClassNotExist" + index.incrementAndGet();
          Class.forName(className);
        } catch (Exception e) {
        }
      }
    }
  }  
  
  public static void main(String[] args) throws Exception {
    for (int i = 0; i < 50; i++) {
      new LoaderWorker().start();
    }
  }

}

```

通过jvisualvm查看线程运行情况
![ClassLoaderJdk6Block.png](/public/img/ParallelCapableClassLoader/ClassLoaderJdk6Block.png)

查看jstack，会发现大量的线程在等待同一个Lock。
![ClassLoaderLockStack.png](/public/img/ParallelCapableClassLoader/ClassLoaderLockStack.png)

### 死锁场景

当多个线程使用不同的class loader 交叉委托加载的时候，极易发生死锁的情况。

```

Class Hierarchy:
  class A extends B
  class C extends D

ClassLoader Delegation Hierarchy:

Custom Classloader CL1:
  directly loads class A 
  delegates to custom ClassLoader CL2 for class B

Custom Classloader CL2:
  directly loads class C
  delegates to custom ClassLoader CL1 for class D

Thread 1:
  Use CL1 to load class A (locks CL1)
    defineClass A triggers
      loadClass B (try to lock CL2)

Thread 2:
  Use CL2 to load class C (locks CL2)
    defineClass C triggers
      loadClass D (try to lock CL1)


```

```

Thread 1:
  Use CL1 to load class A (locks CL1+A)
    defineClass A triggers
      loadClass B (locks CL2+B)

Thread 2:
  Use CL2 to load class C (locks CL2+C)
    defineClass C triggers
      loadClass D (locks CL1+D)

```

线程1执行CL1 加载 Class A时，loaks CL1，线程2执行CL2加载Class C时，lock CL2，此时线程1去加载 B,需要获取CL2锁，而CL2说正被线程2锁持有，而线程2去加载 Class D，需要获取CL1的锁，而CL1锁正被线程1持有。




## JDK 7 Parallel Capable ClassLoader

Parallel Capable 其实是锁的细粒度的使用。

![ClassLoaderJdk7](/public/img/ParallelCapableClassLoader/ClassLoaderJdk7.png)

![ClassLoaderJdk7Lock](/public/img/ParallelCapableClassLoader/ClassLoaderJdk7Lock.png)

在JDK 7 再次运行上面的模拟测试程序，查看形成运行情况，发现大量的block不见。
![ClassLoaderParralMonitor.png](/public/img/ParallelCapableClassLoader/ClassLoaderParallelMonitor.png)


## 参考资料：

 * [Multithreaded Custom Class Loaders in Java SE 7](http://docs.oracle.com/javase/7/docs/technotes/guides/lang/cl-mt.html)




