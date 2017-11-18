---
title: 10个关于Java volatile修饰符和属性的要点
date: 2017-11-15 16:10:52
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2017/02/10-points-about-volatile-modifier-and-field-in-java.html

volatile修饰符对于大部分Java程序员来说都是一个有意思并且棘手的话题。我依然认为它是Java中未被充分使用修饰符之一，如果正确的理解并使用它的话会带到意想不到的好处，毕竟，它为Java带来了一种不需要加锁的同步机制。如果一个属性在多个线程之间共享并且被其中一个线程修改其值，也就是说某个线程读取了被另一个线程写入的属性，那么，通过使用[volatile修饰符](http://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html)，你可以实现对这个属性的同步访问。Java的volatile修饰符保存了**可见性**及**有序性**，并且不依赖锁机制。你需要知道编译器和JVM可能根据某些原因将你的代码重新排序，比如为了提升性能，而其恰恰有可能引发问题。

通过将Java属性声明成volatile，你可以提示编译器、[JVM](http://javarevisited.blogspot.sg/2011/12/jre-jvm-jdk-jit-in-java-programming.html)和[JIT](http://www.java67.com/2013/02/difference-between-jit-and-jvm-in-java.html)不对其进行指令重排，进而阻止发生狡猾的并且难以查找的多线程bug。

同样的，可见证担保能保证内存栅栏会在volatile变量被读取时刷新，因此一个线程中所有写入volatile变量之前的发生的改变都会被另一个试图读取这个volatile变量的线程可见，这也是volatile变量的"happens-before"担保的一部分。

不过，你还是需要小心一点，因为volatile不支持**原子操作**和**相互独立**访问，这也是Java中[同步和volatile关键字的核心不同之处](http://javarevisited.blogspot.com/2011/04/synchronization-in-java-synchronized.html)。因此，volatile变量应该只在变量只有赋值操作的时候使用。

在这篇文章中，我将介绍volatile修饰符对属性的10个重要之处，这将会帮助你在Java多线程世界更好的了解这个会很有用的概念。如果你想了解更多，随时可以阅读Brian Goetz的[Java concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)一书。

## 关于Java volatile修饰符的10点

这里有一些Java开发者应该知道的关于volatile修饰符的有用之处的见解。由于许多人都没有第一手在自己的程序中使用volatile变量的经验，这些见解会帮助你理解一些经验丰富的人已经写过的代码，也就是那些使用volatile修饰符来做同步和线程通讯的代码。

1) volatile修饰符支持属性的**无锁同步机制**。和[同步](http://javarevisited.blogspot.sg/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)关键字不一样，当一个线程想要访问或者写入一个volatile属性时，是不需要获取或释放锁的。

2) volatile修饰符只用用在属性上。你不能在方法或本地变量上使用volatile关键字。当然，你也不需要对本地变量做同步，因为它们不会在线程间共享。每个线程都有它们自己的本地变量复本。

3) 当你将一个Java属性声明为volatile，它会向编译器和JVM发送指令，这个属性可能会被其它线程并发修改。因为这个原因，编译器停止为涉及到[volatile](http://www.java67.com/2012/08/what-is-volatile-variable-in-java-when.html)属性的最大化吞吐量操作重排指令。

4) 除了排序，volatile修饰符还提供可见性保证。任何对volatile变量的修改都对其它线程可见。volatile变量的值不会被线程缓存，反而，它们被总是从主存中读取出来。

5) volatile修饰符还提供**happens-before**保证，对volatile变量的写操作一定会发生在任何后续读操作之前。它还会导致内存栅栏被清空，也就是说所有线程A写入volatile变量之前产生的改变都在线程B读取volatile变量之时可见。一些高性能并发框架，比如LMAX Disrupter利用volatile变量的这个特性来实现无锁同步。

如果你想知道更多有关"happens-before"规则的内容，请阅读Brian Goetz的[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，它里面有一份这份规则所包含的所有内容的清单，以及它们是如果在Java内存模型之下工作的。

<div align=center>[![avatar](https://3.bp.blogspot.com/-PHXJUA_FFSs/WKuKZx8pZQI/AAAAAAAAH9w/534lZ0TR_5si9IpRKSDPndhBc972uU6jwCLcB/s320/Java%2BConcurrency%2Bin%2BPractice%2Bstill%2Brelevant%2Bin%2BJava%2B8%2Bera.jpeg)](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)</div>

6) volatile修饰符支持低消耗同步以替换同步关键字，但是它不能用来替代[同步块](http://www.java67.com/2013/01/difference-between-synchronized-block-vs-method-java-example.html)及同步方法，因为它不支持原子操作和相互独立。低消耗是因为它不需要加锁。线程不会被锁阻塞，也就不需要花时间在获取和释放锁上。

7) 什么时候使用volatile变量对很多Java开发者来说都是最常见的问题，就连经验丰富的开发者也会问到这个问题。原因是因为缺乏用volatile变量来编写并发代码的机会。volatile变量最常见的一种用法是共享布尔标志。这也是高级[Java并发面试问题](一种)中最流行的问题之一。

假设一个对象拥有一个布尔标志，bExit，它由一个线程写入另一个线程读取。在传统游戏循环场景中，玩家可以点击退出按钮来设置bExit的值为true以停止游戏。这里将bExit设置为true的线程就是监听器线程，而读取它的线程则是你的主线程。

在这种情况下，有理由把bExit属性定义为volatile，就像下面这样

{% codeblock lang:java %}
private volatile boolean bExit;

public boolean isExit() { 
   return bExit; 
}

public void setExit(boolean exit){
  this.bExit = exit; 
}
{% endcodeblock %}

另一种volatile属性的常见用法是在实现线程安全的Java单例模式中使用双重检查。如果你不在共享属性上使用volatile，那么主线程见不到监听器线程发生的改变这种情况是有可能产生的。

<div align=center>[![avatar](https://4.bp.blogspot.com/-K_aWJDROPRA/WKuSodKjwdI/AAAAAAAAH-Y/Ma3DYyDtC60egon_mMqVY_VnFWCSd2sSACLcB/s400/java-singleton-pattern-double-checked-locking-volatile.jpg)](http://www.java67.com/2016/04/why-double-checked-locking-was-broken-before-java5.html)</div>

8) 永远记住，volatile发展不提供任何原子操作保证。例如，你不能将计数器定义成volatile，然后设想i++会是原子操作。同样的，修改volatile布尔变量的值也不是原子的。

{% codeblock lang:java %}
8) Always remember, volatile fields do not provide any atomicity guarantee. For example, you cannot make a counter volatile and assume that i++ will be atomic. Similarly, flipping a volatile boolean variable is not atomic
{% endcodeblock %}

如果你需要原子性，你应该使用java.util.concurrent.atomic包中的Atomic类库，比如AtomicInteger可以用来当做并发共享计数器，或者你也可以直接使用同步关键字来包裹语句块。如果你在理解并发基础有麻烦，比如加锁，同步，相互独立，原子性，那我强烈建议你先找一本介绍线程的书来读一读，比如Scott Oaks的[Java Threads: Understanding and Mastering Concurrent Programming](http://www.amazon.com/dp/0596007825/?tag=javamysqlanta-20)。

<div align=center>[![avatar](https://2.bp.blogspot.com/-v6r6tuwYGQw/WKuNEhutYpI/AAAAAAAAH98/HaobKdfcfc4ek_T8NGEGm36aUBzrmCB3gCLcB/s320/Java%2BThreads%2B2nd%2BEdition.jpg)](http://www.amazon.com/dp/0596007825/?tag=javamysqlanta-20)</div>

9) 你应该只将那些只拥有**原子操作**的变量定义为volatile，比如赋值。为boolean和int变量赋值是原子的，但是读取和写入[long](http://www.java67.com/2014/11/how-to-convert-double-to-long-in-java-example.html)和[double](http://javarevisited.blogspot.sg/2016/05/difference-between-float-and-double-in-java.html)是**非原子**的，除非它们是volatile的。所以Java中volatile的另一个作用是将long和double赋值操作变为原子操作。

10) volatile和synchronized修饰符的核心不同之处在于加锁，synchronized关键字需要锁而volatile不需要。因为这个原因，使用volatile变量来做同步的开销要少一些。

synchronized和volatile修饰符的第二个重要不同之处是原子性，synchronized关键字提供原子性保证，并且可以将代码块变成原子操作，而volatile变更不会有这种保证，除了上面讨论过的将Java中long和double的赋值操作变为原子操作。

以上就是所有关于Java的**volatile修饰符**的内容。还有很多东西需要学习，因为Java中编写并发程序并不容易，但是你可以通过了解这些点来刷新你的知识库，更好的理解这个概念。如果你需要同步访问共享变量，变量的值被一个线程写入被另一个线程读取，那你尽可以使用volatile修饰符。它向你提供了synchronized关键字的低消耗替代品，或者说是Java 5引入的锁机制接口，但是不包含原子性及相互独立。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Fundamentals: The Java Language](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369380)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)

其它你可能感兴趣的Java中的10个要点类文章:
Java中线程的10个要点（[阅读](http://www.java67.com/2014/01/10-points-about-thread-and-javalangthread-in-java.html)）
Java中堆空间或堆内存的10个要点（[阅读](http://javarevisited.blogspot.sg/2011/05/java-heap-space-memory-size-jvm.html)）
Java中wait, nofity和notifyAll的10个要点（[查看](http://www.java67.com/2016/04/10-points-about-wait-notify-and-notifyAll-in-java-multithreading.html)）
Java中static修饰符的10个要点（[查看](http://www.java67.com/2012/11/10-points-about-static-in-java.html)）
Java中instanceof操作符的10个要点（[阅读](http://javarevisited.blogspot.sg/2015/12/10-points-about-instanceof-operator-in-java-example.html)）
Java中Enum的10个要点（[阅读](http://www.java67.com/2014/04/what-java-developer-should-know-about-Enumeration-type-in-Java.html)）
Java中finalize方法的10个要点（[教程](http://javarevisited.blogspot.sg/2012/03/finalize-method-in-java-tutorial.html)）
Java中java.lang.String类的10个要点（[教程](http://javarevisited.blogspot.sg/2013/07/java-string-tutorial-and-examples-beginners-programming.html)）

If one thing you can do this week to improve your understanding of multi-threading and concurrency in Java, then you should read Java Concurrency in Practice by Brian Goetz, one of the must-read book for any serious Java developer.
如果你想一周内提升对于多线程的和Java并发知识的理解，那么你应该读一读Brian Goetz的[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，一本每个认真的Java开发者被必须阅读的书。