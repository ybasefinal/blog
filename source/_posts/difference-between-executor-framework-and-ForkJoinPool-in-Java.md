---
title: Java中executor框架与ForkJoinPool的区别
date: 2017-11-20 11:16:18
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html

Java 5为Java程序员加入了一个Executor框架来支持开箱可用的线程池，而java 7则加入了一个ExecutorService的实现ForkJoinPool，这是特别为执行[ForkJoinTask](http://javarevisited.blogspot.sg/2011/09/fork-join-task-java7-tutorial.html)设计的。Executor框架提供了一些类如Executor，ExecutorService和Executors来执行和创建线程池。它还支持几个内置的，现成的线程池，如固定线程数量的线程池，还有[缓存线程池](http://javarevisited.blogspot.sg/2013/07/how-to-create-thread-pools-in-java-executors-framework-example-tutorial.html)，可以在负载过重时根据需要产出新的线程。

上面的线程池的任务是接收任务然后如果有可用的工作线程就执行它，不过ForJoinPool是一种特别的线程池。它们使用的是**任务窃取模式**。所有fork join pool内部的线程都尝试寻找并执行被提交到线程池中及（或）由其它活动任务创建的任务。

这使得当大多数任务产出其它子任务（就如ForkJoinTasks最常做的[递归操作](http://javarevisited.blogspot.sg/2014/04/10-jdk-7-features-to-revisit-before-you.html)），以及许多小任务经由外部客户端提交到线程池中的时候，高效处理成为可能。

简而言之，Executor框架和ForkJoinPool的主要区别是前者提供的是一种**通用用途的线程池**，而后者提供的是一种**特别的实现**，其使用任务窃取模式来高效处理ForkJoinTask。我们来看看一些其它的区别来更好的回答这个问题。


## ForkJoinPool与Executor框架对比

当你要比较ForkJoinPool与Executor框架时，你要么将ForkJoinPool与ThreadPoolExecutor类比较，要么直接与[ExecutorService](http://javarevisited.blogspot.com/2016/04/difference-between-ExecutorServie-submit-vs-Executor-execute-method-in-Java.html)接口比较。

在第一个段落中，我已经对比了ForkJoinPool与ExecutorService，现在我们来看看ForkJoinPool与ThreadPoolExecutor类的一些其它区别。

1) ForkJoinPool与ThreadPoolExecutor的主要区别是ForkJoinPool是专门为接收和执行ForkJoinTask而设计的，是FutureTask的一个轻量级版本，而ThreadPoolExecutor则是为普通线程池设计的，它使用几种可能的池化线程来执行各个提交的任务。

2) ThreadPoolExecutor与ForkJoinPool类的另一个关键区别是ForkJoinPool使用[任务窃取模式](https://en.wikipedia.org/wiki/Work_stealing)，这表示某个线程还可以执行其它线程的挂起任务。这会提高使用ForkJoinTask时候的效率，因为大部分ForkJoinTask算法都会产出大量子任务。你可以深入阅读Brian Goetz的[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QlV3xb7FKIITc5elWxh_GAsAAAFf1y8SbAEAAAFKAWdbGH4/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=Js2xicBEvFKYMAIr.eJYMg&slotNum=2&tag=javamysqlanta-20)来了解更多关于任务窃取模式和其它并行计算模式的内容。

<div align=center>[![avatar](https://2.bp.blogspot.com/-Z7ivhgl4BnA/WEA4H2Ws50I/AAAAAAAAHdw/d5Dhgm9VzqoelDrMd4UIMIB3iWBZS_ZqgCLcB/s400/fork%2Bjoin%2Bthread%2Bframework%2Bjava%2B7.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/QlV3xb7FKIITc5elWxh_GAsAAAFf1y8SbAEAAAFKAWdbGH4/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=Js2xicBEvFKYMAIr.eJYMg&slotNum=1&tag=javamysqlanta-20)</div>

3) JDK API提供了几个常用ThreadPoolExecutor，其一是SingleThreadExecutor，一个只拥有一个线程的线程池，其二是用Executors.newCachedThreadPool创建的缓存线程池，线程数量不限，可以在需要的时候产出新线程，不需要的时候使用旧线程。

第3种常用ThreadPoolExecutor是固定线程数量的线程池，这种线程池在整个生命周期线程数量都是固定的，它也是一个[限定线程池](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html)的例子。



以上就是所有关于**Java中ForkJoinPool与Executor框架的区别**。你应该在使用fork/join框架和ForkJoinTask任务的时候使用ForkJoinPool，否则使用Executors的工厂方法提供了各种各样的ThreadPoolExecutor实例就可以了，比如Executors.newSingleThreadPoolExecutor()，Executors.newCachedThreadPoolExecutor()和Executors.newFixedThreadPoolExecutor()等类。

如果你偶然发现一个其它有用的区别也请让我知晓。如果你想了解更多关于如何高效使用ForkJoin pool，我强烈建议你阅读Brian Goetz的[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QlV3xb7FKIITc5elWxh_GAsAAAFf1y8SbAEAAAFKAWdbGH4/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=Js2xicBEvFKYMAIr.eJYMg&slotNum=2&tag=javamysqlanta-20)，一本任何高级Java开发者都必须阅读的书。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)

其它你可能喜欢的**多线程和并发问题**
Java中线程与进程的区别？（[答案](http://www.java67.com/2012/12/what-is-difference-between-thread-vs-process-java.html)）
Java中多线程和多任务的区别？（[答案](http://www.java67.com/2015/11/difference-between-multithreading-and-multitasking.html)）
Java中同步和可重入锁的区别？（[答案](http://javarevisited.blogspot.sg/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)）
Java中volatile关键字是如何工作的？（[答案](http://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html)）
Java中CyclicBarrier与CountDownLatch的区别？（[答案](http://www.java67.com/2012/08/difference-between-countdownlatch-and-cyclicbarrier-java.html)）
Java中继承Thread和实现Runnable的区别？（[答案](http://javarevisited.blogspot.sg/2012/01/difference-thread-vs-runnable-interface.html)）
Java中线程的start()与run()方法的区别？（[答案](http://www.java67.com/2015/12/difference-between-thread-start-and-run-method-java.html)）