---
title: Java中Executor, ExecutorService和Executers类的不同之处
date: 2017-11-14 22:59:37
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2017/02/difference-between-executor-executorservice-and-executors-in-java.html

Executor, ExecutorService, 和Executers三者都属于Java的[Executor框架](http://javarevisited.blogspot.sg/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html)，它们为Java程序提供了**线程池**的功能。由于创建及维护线程开销很大并且操作系统还会对程序可以产生多少线程有限制，所以更好的办法应该是使用线程池并行执行任务，而不是每处理一个请求就创建一个纯种。这不旦能加速程序响应时间，还是阻止诸如"java.lang.OutOfMemoryError: unable to create new native thread"之类的资源耗尽问题。[线程池](http://javarevisited.blogspot.sg/2013/07/how-to-create-thread-pools-in-java-executors-framework-example-tutorial.html)在程序启动的时候就创建了，它能同时解决上面的这些问题。它会在客户端需要的时候提供一个准备好的线程，同时可以设置最多能创建多少线程。

在Java1.5之前，创建及管理线程池由程序开发者负责，但从JDK5开始Executor框架推出了多套内置线程池，比如包含一个固定线程数量的固定线程池以及在需要时可以产生新线程的缓存线程池。

Executor, ExecutorService, 和Executors类的主要区别在于Executor是核心接口，它是针对并行执行的抽象。它将任务从执行时分隔出来，这与java.lang.Thread将2者绑在一起的方式是不同的。你可以读一读[Thread和Executor的不同之处](http://javarevisited.blogspot.sg/2016/12/difference-between-thread-and-executor.html)来了解更多这2个Java关键类的不同。

另一方面，ExecutorService是对Executor接口的一个扩展，它提供了返回一个Future对象的功能，并且可以终止或者关闭线程池。一旦调用了shutdown，那么线程池不会再接收新任务请求，只会完成其它未完成的任务。它还提供了一个[submit()](http://javarevisited.blogspot.sg/2016/04/difference-between-ExecutorServie-submit-vs-Executor-execute-method-in-Java.html)方法，针对Exceutor.execute()方法做了扩展，在其之上返回一个Future。

Future对象的功能是用来做异步执行，也就是说你不需要等到执行写成，你可以在提交任务后去做其它事情，过后检查Future对象是否已经有了结果，如果执行完成了那你就可以用Future.get()方法获取结果。另外记住这个方法是[阻塞方法](http://javarevisited.blogspot.sg/2012/02/what-is-blocking-methods-in-java-and.html)，也就是说如果还没有执行完成它会等到执行完成得出结果。

通过使用ExecutorService.submit()方法返回的[Future对象](http://javarevisited.blogspot.sg/2015/01/how-to-use-future-and-futuretask-in-Java.html)，你还可以取消不希望再执行的任务。它提供了cancel()方法来取消任何未完成的任务。

第三个类Executors是一个类似于Collections的工具类，它提供了可以创建不同类型线程池的工厂方法，比如固定线程池和缓存线程池。我们再来仔细看看这三者的不同之处。

## Java的Executor类、ExecutorService类和Executors类的比较

就如我说的，这三个类都是Java 1.5 Executor框架的一部分，Java程序员需要学习并理解这些类，进而可以高效利用Java提供的不同类型的线程池，这一点很重要。为了更好的理解它们，我们来看看Java中Executor, ExecutorService和Executors的关键不同之处：

1) Executor与ExecutorService接口的一个关键不同之处在于前者是父接口而ExecutorService则继承自Executor，也就是说它是Executor的一个子接口。

2) 另一个ExecutorService与Executor的重要区别是Executor定义的execute()方法，接受一个Runnable接口的对象作为参数，而submit()方法则能接受[Runnable](http://www.java67.com/2016/01/7-differences-between-extends-thread-vs-implements-Runnable-java.html)接口或者[Callable](http://javarevisited.blogspot.com/2016/08/useful-difference-between-callable-and-Runnable-in-Java.html)接口的对象作为参数。

3) Executor和ExecutorService接口的第3个不同点是execute()方法不返回任何结果，它们返回类型是void，而submit()方法将计算结果通过[Future](http://javarevisited.blogspot.com/2015/06/how-to-use-callable-and-future-in-java.html)对象返回。这也是submit()和execute()方法的核心不同之处，也是[Java并发面试问题](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html)中最常被问到的地方。

4) ExecutorService和Executor的第4个不同点是除了允许调用者提交任务，ExecutorService还支持可以控制线程池的方法，比如调用shutDown()方法来终止线程池。你还应该读一读["Java Concurrency in Practice"](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)来了解更多如果更优雅的关闭线程池以及如何处理未完成的任务。

<div align=center>[![avatar](https://1.bp.blogspot.com/-G4QU9Qx4j-c/WLYGAMfR3NI/AAAAAAAAICs/yy6Zk9buUS4VTJF-m5TzbA66qQzGpuxvACLcB/s320/Java%2BConcurrency%2Bin%2BPractice%2Bstill%2Brelevant%2Bin%2BJava%2B8%2Bera.jpeg)](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)</div>

5) Executors类提供了工厂方法来创建不同类型的线程池，比如newSingleThreadExecutor()可以创建只包含一个线程的线程池，newFixedThreadPool(int numOfThreads)可以创建一个固定线程数量的线程池，而newCachedThreadPool()则会创建一个在需要时可以生成新的线程的线程池但会优先使用已存在的可用线程。

## 总结

这里是对Java中Executor与ExecutorService不同之处做的总结：

<div align=center>[![avatar](https://4.bp.blogspot.com/--CAPMy-yet0/WLYI_SQvMuI/AAAAAAAAIC0/VyDHWmgCwuw38ZTyF2o_MiiCFwhbt78_wCLcB/s640/Difference%2Bbetween%2BExecutor%2Band%2BExecutorService%2Bin%2BJava.png)](https://4.bp.blogspot.com/--CAPMy-yet0/WLYI_SQvMuI/AAAAAAAAIC0/VyDHWmgCwuw38ZTyF2o_MiiCFwhbt78_wCLcB/s1600/Difference%2Bbetween%2BExecutor%2Band%2BExecutorService%2Bin%2BJava.png)</div>

以上就是所有关于**Java中Executor, ExecutorService, 和Executors的不同之处**。记住，三者都属于Executor框架。Java提供了一些开箱即用的线程池实现，比如固定线程数量的线程池以及根据负载需要实时扩展的线程池。

ExecutorService类的submit()方法支持返回计算结果，Executor的execute()方法的参数是可以被同一个线程、线程池或其它线程执行的任务，具体依赖于实现。你可以深入读一读["Java Concurrency in Practice"](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)来了解更多关于Java线程池的知识。