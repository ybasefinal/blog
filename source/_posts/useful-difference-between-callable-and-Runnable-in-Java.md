---
title: Java中callable和Runable的实用区别
date: 2017-11-21 17:31:17
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/08/useful-difference-between-callable-and-Runnable-in-Java.html

Callable和Runnable的区别是Java面试中通常被问到的一个问题。我记得，我是在2007年第一次听到关于Callable接口的，而那次正好是一次电话面试。在那之前，我满足于使用Runnable来实现线程并且刚刚开始关注Java 1.5，因为大多数程序在那里还只是基于Java 1.4。就是那一个面试问题鼓动了我学习更多Java 5并发库新引入的几个其它实用特性，如CountDownLatch，CyclicBarrier，Semaphore，原子变量，和线程池。这也是我总是怂恿Java开发者给予或是取得常规面试的一个原因，只是为了更新你自己的知识库。

这篇文章里，我将通过列举一些Runnable和Callable在Java中的关键区别来讨论如何回答这个问题。之前，我还没有全部了解这些知识点，后来我的研究帮助我了解了更多关于**Callable**接口的知识。

尽管[Callable](http://www.java67.com/2013/01/difference-between-callable-and-runnable-java.html)和[Runnable](http://www.java67.com/2012/08/what-is-thread-and-runnable-in-java.html)接口都是试图将任务封装起来给另一个线程来执行，但是它们之间有2点关键区别：
1. Callable可以返回结果
2. Callable可以抛出checked异常

实际上，Java 1.5引入Callable接口就是为了解决Runnable接口的上面2个限制，也就是Runnable不能返回计算结果，这是一个将计算任务交给其它线程执行时的基本需求，另外Runnable不能抛出checked异常。现在，Java的多线程API已经在2者上都有了好转。可你以深入阅读Cay S. Horstmann的[Core Java Volume 1 - Fundamentals](http://aax-us-east.amazon-adsystem.com/x/c/QkSqZX-jpqzvPYQKGyTnHYEAAAFf3eqEhwEAAAFKAQtWY7g/https://www.amazon.com/Core-Java-I--Fundamentals-10th/dp/0134177304/ref=as_at?creativeASIN=0134177304&linkCode=w61&imprToken=G7c8eQLkPjbNqV9tU43j.A&slotNum=0&tag=javamysqlanta-20)来了解更多关于使用多线程编写Java程序时什么时候该使用Runnable，什么时候使用Callable。

如我据说，Callable和Runnable有什么区别也是一个Java面试经常被问到的问题，那么知道它们的区别是不是不仅可以帮你写出更好的代码，还会帮你顺利通过面试呢？我们来看看这2个接口的一些其它区别来更好的理解它们。



## Runnable与Callable对比

在考虑Runnable和Callable接口之间的区别之前，我们先来看看它们之间的相似点，它们的确非常相似。

1) 它们之间最共同的一点是2者都是用来封装代码，代码封装后需要由一个单独的线程并行运行。

2) 它们之间的第2个相似点是2者都可以被Java 5的Executors框架使用。[Executors框架](https://javarevisited.blogspot.com/2013/07/how-to-create-thread-pools-in-java-executors-framework-example-tutorial.html)定义的ExecutorService接口可以接收并执行Runnable或Callable。

3) 你还可以用Executor类提供的下列工具方法将Runnable转换为Callable。

thread, just like you execute a Runnable instance, you need an ExecutorService instance. Let's see them in detail.
如我据说，2者都用于封装代码，你需要用一个单独的线程来执行它们，但是这里有一个限制，你无法像执行一个Runnable实例那样在线程中执行一个[Callable实例](http://javarevisited.blogspot.com/2015/06/how-to-use-callable-and-future-in-java.html)，你需要一个ExecutorService接口。让我们详细地看一下。

{% codeblock lang:java %}
Callable callable = Executors.callable(Runnable task);
{% endcodeblock %}

4) 第4个Runable和Callable接口的相似点是它们都是SAM类型，也就是它们都只有一个抽象方法，这表示它们可以在[Java 8的lambda表达式](http://javarevisited.blogspot.com/2014/02/10-example-of-lambda-expressions-in-java8.html)内使用，如下：

{% codeblock lang:java %}
new Thread( () -> System.out.println("Runnable") ).start()
{% endcodeblock %}

现在，我们知道了Runnable和Callable接口之间的相似类，那么是时候看看它们之间的区别了。你还可以用[Java Programming Interview exposed](http://aax-us-east.amazon-adsystem.com/x/c/QkSqZX-jpqzvPYQKGyTnHYEAAAFf3eqEhwEAAAFKAQtWY7g/http://www.amazon.com/Java-Programming-Interviews-Exposed-Markham/dp/1118722868/ref=as_at?creativeASIN=1118722868&linkCode=w61&imprToken=G7c8eQLkPjbNqV9tU43j.A&slotNum=1&tag=javamysqlanta-20)这本书检验类似的问题来多加练习。


<div align=center>[![avatar](https://1.bp.blogspot.com/-ck5AA7iUWPw/V7gON2JxlII/AAAAAAAAG2c/ceP7zwPHqSE6lKGzgnNEQPq5L5h-WJwLQCEw/s320/Java%2BProgramming%2BInterview%2BExposed.png)](http://aax-us-east.amazon-adsystem.com/x/c/QkSqZX-jpqzvPYQKGyTnHYEAAAFf3eqEhwEAAAFKAQtWY7g/http://www.amazon.com/Java-Programming-Interviews-Exposed-Markham/dp/1118722868/ref=as_at?creativeASIN=1118722868&linkCode=w61&imprToken=G7c8eQLkPjbNqV9tU43j.A&slotNum=2&tag=javamysqlanta-20%22)</div>

## Runnable与Callable接口的区别

这里用几个小点的形式来列出这2个Java多线程API的核心接口的重要区别。

1) 存在状况和可用性
首先Runnable和Callable接口最重要的区别是Runnable接口从Java推出的时候就已经存在了，也就是JDK 1.0，而Callable则是在Java 5的时候加进来的，所以你不能在Java 5之前使用Callable。

2) 结果
Callable和Runnable的第2点关键不同是Callable可以返回并行任务处理的结果。它返回一个[Future对象](http://javarevisited.blogspot.com/2015/01/how-to-use-future-and-futuretask-in-Java.html)，这个对象代表了这个任务的生命周期，并且提供了可以检查任务是否完成或取消，以及用来获取结果或取消执行的方法。而由于Runnable的run()方法是没有返回类型的，所以它也不能返回任务内容。

3) checked异常
这2个接口之间另一个值得注意的区别是Callable的call()方法可以抛出[checked异常](http://www.java67.com/2012/12/difference-between-runtimeexception-and-checked-exception.html)，而Runnable的run()方法不能抛出checked异常。

4) call()方法与run()方法对比
为了要使用Callable，你需要重写call()方法，而要使用Runnable接口的话，你需要重写run()方法。

5) 执行
使用Callable接口的时候有一点限制，你不能像Runnable实例那样将Callable实例传给Thread。Thread类中没有定义可以接收Callable实例的构造方法。所以为了使用Callable实例你需要使用Java 5 Executor框架的ExecutorService接口。这个接口定义的[submit()方法](http://javarevisited.blogspot.com/2014/07/top-50-java-multithreading-interview-questions-answers.html)可以接收一个Callable实例并且返回一个Future对象，这个对象会持有计算的结果，具体如下：

{% codeblock lang:java %}
Future result = exec.submit(aCallable);
result = holder.get();
{% endcodeblock %}

记住，Future.get()是一个阻塞方法，它会一直阻塞到执行完成，所以你应当总是延时调用这个方法来避免在你的程序中产生[死锁](http://javarevisited.blogspot.com/2010/10/what-is-deadlock-in-java-how-to-fix-it.html)或活锁。

简而言之，这里是Java中Runnable和Callable的关键区别：
<div align=center>[![avatar](https://1.bp.blogspot.com/-IRfXhenmstw/V7gO3suJKKI/AAAAAAAAG2o/W5dodMsUQKkE3OaT8p7qnprHNOU2UdNpgCLcB/s1600/Callabe%2Bvs%2BRunnable%2Bin%2BJava.jpg)](https://1.bp.blogspot.com/-IRfXhenmstw/V7gO3suJKKI/AAAAAAAAG2o/W5dodMsUQKkE3OaT8p7qnprHNOU2UdNpgCLcB/s1600/Callabe%2Bvs%2BRunnable%2Bin%2BJava.jpg)</div>

以上就是所有关于**Java中Callable和Runnable接口之间的不同之处**。除了基本事实上Callable是在Java 5中加入的，你还应该记住Callable可以返回结果和抛出checked异常，并且你需要使用ExecutorService来执行Callable实例。一点好处是Runnable和Callable都是SAM类型，也就是说你可以在lambda表达式中使用它们。

你可能喜欢的类似**经常问到的线程面试题**
Java中start()和run()方法的区别？（[答案](http://www.java67.com/2015/12/difference-between-thread-start-and-run-method-java.html)）
Java中Executor的execute()和submit()方法的区别？（[答案](http://javarevisited.blogspot.com/2016/04/difference-between-ExecutorServie-submit-vs-Executor-execute-method-in-Java.html)）
Java中如果联合多线程？（[答案](http://javarevisited.blogspot.com/2013/02/how-to-join-multiple-threads-in-java-example-tutorial.html)）
Java中如何停止线程？（[答案](http://javarevisited.blogspot.com/2011/10/how-to-stop-thread-java-example.html)）
Java中如果使用阻塞队列来解决生产者消费者问题？（[解决方案](http://www.java67.com/2015/12/producer-consumer-solution-using-blocking-queue-java.html)）
为经验丰富的程序员准备的50个顶级面试题（[列表](http://javarevisited.blogspot.com/2014/07/top-50-java-multithreading-interview-questions-answers.html)）

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)