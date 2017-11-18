---
title: thread与executor的区别
date: 2017-11-18 18:12:51
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/12/difference-between-thread-and-executor.html

尽管Thread和Executor都可以用来并行执行代码，但是它们还是有一些关键不同之处。它们的主要区别是后台为Java带来了线程池。JDK5引入了大量并发工具如[CountDownLatch](http://www.java67.com/2015/06/java-countdownlatch-example.html)， [CyclicBarrier](http://www.java67.com/2015/06/how-to-use-cyclicbarrier-in-java.html)， Semaphore， FutureTask， Callable接口，和Conditions，另外也引入了线程池，可以让你的代码在一套工作线程中并行执行。

JDK 1.5的[Executor框架](http://javarevisited.blogspot.com/2013/07/how-to-create-thread-pools-in-java-executors-framework-example-tutorial.html)是Executor, Executors, 和[ExecutorService](http://javarevisited.blogspot.com/2016/04/difference-between-ExecutorServie-submit-vs-Executor-execute-method-in-Java.html)接口的合成体，为Java提供了一个全功能的，特性丰富的线程池。另外这也是Java中Thread和Executor概念的基本不同之处，为了更好的回答这个问题，我们来看一些更详细的细节。

## Java Thread与Executor对比

如我据说，Thread是用来并行运行你的代码的，你可以通过继承java.lang.Thread类或实现java.lang.Runnable接口来创建并启动你的线程。尽管2者都可以在小程序中正常工作，但是它们各自都有[利弊](http://www.java67.com/2016/01/7-differences-between-extends-thread-vs-implements-Runnable-java.html)之处，你将会在这里看到。另一方面，Executor接口也可以提供并行执行，但是是通过线程池的，它更适合用来开发大型Java程序。

1) 首先，Thread和Executor之间的区别是java.lang.Thread是一个[类](http://www.java67.com/2016/08/difference-between-class-and-interface-in-java.html)而java.util.concurrent.Executor是一个[接口](http://www.java67.com/2014/02/what-is-actual-use-of-interface-in-java.html)。

2) Executor的概念实际上是对并行计算提取的抽象层。它允许并发代码通过多种方式运行。而另一方面，用Thread运行的并行代码更细节化。

3) Executor和一个Thread类之间的第3个区别是前者将任务（需要并行执行的代码）与执行**解耦**，而在Thread中，任务和执行是**紧密结合**的。你可以深入阅读Brian Goetz的[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)来了解更多关于为什么将任务从执行中分离出来会简化Java的并发程序设计。

4) Executor概念允许你的任务被线程池中的某个工作线程来执行，而Thread的话则是自身来执行任务。

<div align=center>[![avatar](https://2.bp.blogspot.com/-szF6-jChXHY/WFlVLJ_GOpI/AAAAAAAAHjM/GAqMVNnFkCw0ERRdodxA7Vel2P7FYLvKgCEw/s1600/Executor%2BFramework%2Bin%2BJava.jpeg)](https://2.bp.blogspot.com/-szF6-jChXHY/WFlVLJ_GOpI/AAAAAAAAHjM/GAqMVNnFkCw0ERRdodxA7Vel2P7FYLvKgCEw/s1600/Executor%2BFramework%2Bin%2BJava.jpeg)</div>

5) Executor提供一个execute()方法，接收Runnable任务，而Thread将[Runnable任务](http://javarevisited.blogspot.sg/2012/01/difference-thread-vs-runnable-interface.html)做为自身的构造参数。

6) Thread和Executor的另一个关键不同是Thread只能运行**一个Runnable任务**，而Executor没有这方面限制。

7) 如果使用Thread，那么任务是被接收Runnable实例的线程来执行的，而要是使用Execution的话，命令（Runnable的实例）可能是被一个新的线程来执行，至于是池化线程还是调用者线程自身，取决于Executor接口的实现。

8) 使用线程的话，**由开发者负责创建和启动线程**，而使用Executor的话，框架会为你创建和启动线程。尽管你可以通过提供自己的Executor实现来完全控制这个处理过程。不过，随着Java 7和8的ForkJoinPool带来的提升，你可能会更想要使用它来替代Executor。如果ForkJoinPool对你来说是一不念旧恶全新的概念，我建议你阅读[Java 8 in Action](http://www.amazon.com/Java-Action-Lambdas-functional-style-programming/dp/1617291994?tag=javamysqlanta-20)来了解一下它。

<div align=center>[![avatar](https://2.bp.blogspot.com/-y1mzHX_PZrw/WFlV0WBieiI/AAAAAAAAHjQ/kxksLIAJyp4Z6DZc0v6zfIHxRLrQ2GfDgCLcB/s320/Java%2B8%2Bin%2BAction%2Bbest%2Bbook%2Bto%2Blearn.jpg)](http://www.amazon.com/Java-Action-Lambdas-functional-style-programming/dp/1617291994?tag=javamysqlanta-20)</div>

9) 现在，我们来看看一个Java中使用Executor运行任务和使用Thread运行任务的对比例子：

{% codeblock lang:java %}
import java.util.concurrent.Executor;
import java.util.concurrent.Executors;

public class Main {

  public static void main(String args[]) {

    Runnable task = new Runnable() {
      @Override
      public void run() {
        System.out.println("Task is executed by : "
            + Thread.currentThread().getName());
      }
    };

    Thread t = new Thread(task, "MY_THREAD");
    t.start();

    Executor e = Executors.newSingleThreadExecutor();
    e.execute(task);

  }
}

Output
Task is executed by MY_THREAD
Task is executed by pool-1-thread-1
{% endcodeblock %}

区别很清晰，第一个是使用线程而后面的是使用线程池。

值得注意的是Executors类的工厂方法，比如newSingleThreadExecutor()返回一个ExecutorService，这是Executor的子接口，另外它支持[回调](http://javarevisited.blogspot.com/2016/08/useful-difference-between-callable-and-Runnable-in-Java.html)，并可以终止和停止线程池。

以上就是所有关于Java Thread与Executor之间的不同之处。你可以看见尽管它们都和并行执行有关，但是它们属于不同的层面。Thread代表一些负责并行执行你的代码的东西，而Executor则是执行并发任务的抽象层。最重要的是，Executor将任务与其执行解耦，这意味着异常执行是有可能的，而Thread中的任务和执行则紧密结合在一起。

其它你可能喜欢的多线程文章
过去5年内的50个顶级的Java多线程面试题（[列表](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html)）
Java开发者应该遵循的10个多线程和并发最佳实践（[文章](http://javarevisited.blogspot.com/2015/05/top-10-java-multithreading-and.html)）
Java中如何使用Future和FutureTask？（[教程](http://javarevisited.blogspot.com/2015/01/how-to-use-future-and-futuretask-in-Java.html)）
如何使用Lock和Condition来解决生产者消费者问题（[解决方案](http://javarevisited.blogspot.com/2015/06/java-lock-and-condition-example-producer-consumer.html)）
"Java Concurrency in Practice"还适用于Java 8吗？（[见解](http://javarevisited.blogspot.sg/2016/12/is-java-concurrency-in-practice-still-relevant-in-era-of-java8.html)）
Java中如何合并多于2个的线程？（[例子](http://www.java67.com/2015/07/how-to-join-two-threads-in-java-example.html)）
Java中如何正确停止一个线程？（[教程](http://www.java67.com/2015/07/how-to-stop-thread-in-java-example.html)）

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)