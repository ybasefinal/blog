---
title: Java中ExecutorServie.submit()与Executor.execute()方法的区别
date: 2017-11-24 22:09:23
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/04/difference-between-ExecutorServie-submit-vs-Executor-execute-method-in-Java.html

Java中ExecutorServie.submit()与Executor.execute()方法有什么区别？对于经验丰富的Java程序员来说这是一个[很好的多线程问题](http://javarevisited.blogspot.com/2014/07/top-50-java-multithreading-interview-questions-answers.html)，通常在投资银行如Barclays，Deutsche Bank，或Citibank被问到。submit()与execute()方法的主要区别是ExecuterService.submit()可以返回计算结果因为它有的返回类型是[Future](http://javarevisited.blogspot.com/2015/01/how-to-use-future-and-futuretask-in-Java.html)，而execute()方法不能返回任何内容因为它的返回类型是void。Java 1.5的executor框架的核心接口就是Executor接口，它定义了execute(Runnable task)方法，这个方法的主要目的是为了将任务与执行分离开来。

任何被提交到Executor的任务都可以被同一个线程，线程池中的工作线程或者其它线程来执行。

另一方面，submit()方法被定义在ExecutorService接口中，这是Executor的子接口，前者在后者之上添加了可以终止线程池的功能，以及一个额外的可以接收一个Callable任务做为参数并返回一个计算结果的submit()方法。

查看[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/Qk-zWBRWpPTcftc4SJDrSPcAAAFf7lxDYwEAAAFKAZETCc0/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=15MM.jASRSW.z6ne-ueMJw&slotNum=0&tag=javamysqlanta-20)了解更多关于线程池如何工作以及在不同类型的线程池中任务是如何分配给线程的内容。

<div align=center>[![avatar](https://2.bp.blogspot.com/-sktqs3qhJfM/VxZAgP6FWAI/AAAAAAAAFtg/31XImIxwAhU7Km2YAbEnMs3CfrICK4fVwCLcB/s320/Java%2BConcurrency%2Bin%2BPractice.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/Qk-zWBRWpPTcftc4SJDrSPcAAAFf7lxDYwEAAAFKAZETCc0/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=15MM.jASRSW.z6ne-ueMJw&slotNum=1&tag=javamysqlanta-20)</div>

这就是submit()与ExecutorService的execute()方法的基础区别，我们来看看一些其它的地方来更好的回答这个问题。


# Executor.execute()与ExecutorService.submit()方法

如我在第一段落所说，execute()与submit()方法的关键区别是前者无法返回结果而后者可以返回计算结果。在探讨更多区别之前，我们先来看看execute()与submit()之间还有什么相似点：

1) submit()与execute()方法都可以用于将任务提交给[Executor框架](http://javarevisited.blogspot.com/2013/07/how-to-create-thread-pools-in-java-executors-framework-example-tutorial.html)做异步执行。

2) submit()与execute()都可以接收一个Runnable任务。

3) 你可以通过ExecutorService接口访问submit()和execute()，因为它继承了定义了exeute()方法的Executor接口。

除了submit()方法可以返回结果而execute()不能这个事实之外，下面就是一些Java 5里Executor框架的这2个核心方法其它值得讨论的区别。

1) submit()既可以接收[Runnable](http://java67.blogspot.com/2016/01/7-differences-between-extends-thread-vs-implements-Runnable-java.html)任务又可以接收[Callable](http://javarevisited.blogspot.com/2015/06/how-to-use-callable-and-future-in-java.html)任务，而execute()只能接收Runnable任务。

2) submit()方法定义在ExecutorService接口中，而execute()方法定义在Executor接口中。

3) submit()方法的返回类型是Future，而execute的返回类型是void。

顺道一提，Cay S. Horstmann的经典[Core Java Volume 1 - Fundamentals](http://aax-us-east.amazon-adsystem.com/x/c/Qk-zWBRWpPTcftc4SJDrSPcAAAFf7lxDYwEAAAFKAZETCc0/http://www.amazon.com/Volume-II-Advanced-Features-Edition-Series/dp/013708160X/ref=as_at?creativeASIN=013708160X&linkCode=w61&imprToken=15MM.jASRSW.z6ne-ueMJw&slotNum=2&tag=javamysqlanta-20)第10版中也覆盖了这个必要话题。你可以就这个话题深入阅读那本书。

<div align=center>[![avatar](https://4.bp.blogspot.com/-m2d4XyCHdbg/VxZCWZH1IJI/AAAAAAAAFts/fBl-dZNndo4-sjN-j1vOIHMuHbK_oFwXgCLcB/s320/Core%2BJava%2BVolume%2B1%2B10th%2BEdition.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/Qk-zWBRWpPTcftc4SJDrSPcAAAFf7lxDYwEAAAFKAZETCc0/http://www.amazon.com/Volume-II-Advanced-Features-Edition-Series/dp/013708160X/ref=as_at?creativeASIN=013708160X&linkCode=w61&imprToken=15MM.jASRSW.z6ne-ueMJw&slotNum=3&tag=javamysqlanta-20)</div>

# 什么时候在Java中使用submit()和execute()方法

一旦你理解了Executor.execute()与ExecutorService.submit()方法之间的区别，那么你就拥有可以决定什么时候使用sumbmit()方法以及execute()方法的知识了。

一般而言，如果你所做的是计算任务，比如计算一些风险数据，[计算大数字的阶乘](http://java67.blogspot.com/2015/09/how-to-use-biginteger-class-in-java.html)或者执行一些消耗时间的计算并最终返回某些结果，那么就使用submit()方法。它会直接返回一个Furture对象，稍后可以调用它的get()方法来查询计算结果。

记住，get()是一个[阻塞调用](http://javarevisited.blogspot.com/2012/02/what-is-blocking-methods-in-java-and.html)，所以永远只调用那个接收一个延时参数的方法版本。而当你仅仅只想让代码被线程池的工作线程并行执行的时候，你就可以使用execute()方法。

这里是对于Java中submit()与execute()方法之间关键区别的一个不错的总结：

ExecutorService.submit() vs Executor.execute()


以上就是所有关于**Java中ExecutorService.submit()与Executor.execute()方法之间的区别**。记住，它们的关键区别是submit()可以返回一个Future而execute()不返回任何东西。

用Executors类创建线程池的同时也返回一ExecutorService的引用，它可以同时用于访问submit()和execute()方法，因为它也是继承的Executor接口，也就是executor()方法的来源。

如果你在执行计算任务如计算饼图的值就使用submit()，而如果你仅仅想让代码在线程池的工作线程里执行就使用exeute()。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)