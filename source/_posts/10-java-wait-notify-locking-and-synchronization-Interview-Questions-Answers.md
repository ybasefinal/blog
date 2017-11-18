---
title: 10个顶级java等待、唤醒、加锁和同步的面试题
date: 2017-11-16 13:36:07
tags:
---
翻译自：http://javarevisited.blogspot.ca/2017/02/10-java-wait-notify-locking-and-synchronization-Interview-Questions-Answers.html

关于如何在Java中使用wait和notify，如何使用它们，什么时候使用同步，以及如何在Java中编写并发代码，已经有了大量文章和书籍，但不幸的是，我仍然遇到过很多Java程序员，挣扎在解决经典的[生产者-消费者](http://javarevisited.blogspot.com/2015/07/how-to-use-wait-notify-and-notifyall-in.html)问题的泥潭中。我知道，编写正确的并发代码是非常具有挑战性的，而且需要凭借能力和经验大量的练习，思考才能走向正途，但与此同时，每个Java程序员都需要对线程通讯的基础、同步和加锁熟悉，并弄清楚它们是如何工作在一起的。他们需要能够解释清楚同一时间同一代码块被多个线程执行时的输出内容。他们需要知道编译器会对他们的代码做指令重排以取得性能提升，以及如何阻止重排的发生。

因为多数Java程序员已经对wait，notify，加锁，和同步概念有了一些清楚的想法，所以这里我不会去解释它们，而是通过呈现出一些面试题来检验和挑战你的理解。如果你能自信的正确回答这些问题并且不让自己产生困惑，那么你就很安全了，否则，你应该重新读一读[Effective Java](http://www.amazon.com/dp/0321356683/?tag=javamysqlanta-20)和[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)。

## 10个Java加锁，同步和线程通讯问题

这里是我整理的一些关于加锁，同步，线程通讯以及关于Java的wait，notify和notifyAll方法的问题清单。

### 你什么时候在Java中使用同步？
首先，做为一个Java程序员，你应该清楚的知道什么时候你的代码需要同步。这个问题的答案可以在Brian Goetz创造的“**同步格言**”中找到，“如果你写入了一个接下来可以被另一个线程读取的变量，或者你正在读取一个刚刚被另一个线程写入的变更，那么你应该使用同步”。

假设你的代码如下：

{% codeblock lang:java %}
while(!closed){
  printf("Yes, losing weigth is possible");
}
{% endcodeblock %}

这里的closed是一个布尔属性，它可能会被另一个线程修改，而当用户按下一个按钮时被运行这个循环的线程读取。这种情况下，你需要某种同步机制，以保证一个线程做出手改变能被其它线程看见。

你可以通过使用Java的[synchronized](http://javarevisited.blogspot.com/2011/04/synchronization-in-java-synchronized.html)或者[volatile](http://www.java67.com/2012/08/what-is-volatile-variable-in-java-when.html)关键字来实现同步。其实，这种情况最适合使用volatile属性，因为它提供了低开销同步机制。

有一些情况下，就算有多个线程在执行同样的代码你也不需要使用同步，比如从安全初始化后的HashMap取值。其取值操作是线程安全操作不需要同步就可以完成。如果你要取的值在初始化后就不会发生变化，比如[final变量](http://javarevisited.blogspot.sg/2016/09/21-java-final-modifier-keyword-interview-questions-answers.html)。

所以，不要在面试的时候说一旦代码被多个线程执行你就需要同步，你还需要考虑代码所做的事情。如果一个线程读而另一个线程写，那么你才肯定需要同步来保证你想要的输出。

### 什么时候在Java中使用wait和notify？
Java的wait()，notify()和notifyAll()方法是用来做[线程通讯](http://javarevisited.blogspot.com/2013/12/inter-thread-communication-in-java-wait-notify-example.html)的工具。另一个类似的工具集是从Java 5引入的await()，signal()和signalAll()，它们和[Lock和Condition](http://javarevisited.blogspot.com/2015/06/java-lock-and-condition-example-producer-consumer.html)对象工作在一起。当你的程序拥有多个线程并且彼此需要互相通讯的时候你会需要这些工具。最好的例子还是那个生产者-消费者问题，两个线程需要告知彼此的动作，因为它们可能会影响彼此。

例如，如果共享队列是空的并且消费者在等待（你可以使用wait()方法或Java 5的condition.wait()将线程根据某种条件变为等待状态）新条目加入到队列中，生产者可以在成功插入后通知消费者。一旦消费者收到通知，它就可以重新启动它的任务了。查看[这里](http://www.java67.com/2012/12/producer-consumer-problem-with-wait-and-notify-example.html)以了解更多关于如果在Java中使用wait和notify来解决生产者消费者问题。

<div align=center>[![avatar](https://3.bp.blogspot.com/-bpXZmJflIi0/WJkZ_HBTk_I/AAAAAAAAH34/F1xEK3UxoOYTzY6AU9m1OlOubDao-f92wCLcB/s400/Producer%2BConsumer%2Bin%2BJava.png)](https://3.bp.blogspot.com/-bpXZmJflIi0/WJkZ_HBTk_I/AAAAAAAAH34/F1xEK3UxoOYTzY6AU9m1OlOubDao-f92wCLcB/s1600/Producer%2BConsumer%2Bin%2BJava.png)</div>

### Java中的notify和notifyAll有什么区别？
在我回答这个问题之前，给你一个日常忠告，尽量留心命名，因为它们通常会揭示其用途。就如命名所暗示的，[notify()](http://www.java67.com/2013/03/difference-between-wait-vs-notify-vs-notifyAll-java-thread.html)方法只给多个等待线程中的一个发送通知，而notifyAll()则会将通知发送给所有等待线程。

那么，如果是使用notify()，那个线程会收到通知呢？答案是这个选择是随机的。这也是一个[狡猾的多线程问题](http://www.java67.com/2012/09/top-10-tricky-java-interview-questions-answers.html)，接下来你可能会被问到很多和这个问题相关的内容，所以确保你的回答是正确的，因为下个问题中你会用到它。

### 为什么使用notifyAll()比使用notify是一个更安全的选项？
如果因为某些原因，那个被随机选中的接收notify()方法通知的线程不能继续执行而重新开始等待，那么你的程序就不会往更深一步发展了。如果调用notify()的线程在发送通知后进入等待状态，你的程序可能被完全[死锁](http://javarevisited.blogspot.sg/2010/10/what-is-deadlock-in-java-how-to-fix-it.html)，因为不会再有运行中的线程来唤醒所有等待中的线程了。

<div align=center>[![avatar](https://2.bp.blogspot.com/-gKUTABiCfAc/WJkbDmcG-CI/AAAAAAAAH4E/5FLcOOVsmkAq2Xz23KfZhi6qm2_i8EYzwCLcB/s1600/notify%2Bvs%2Bnotifyall%2Bmethod%2Bin%2BJAva.jpg)](https://2.bp.blogspot.com/-gKUTABiCfAc/WJkbDmcG-CI/AAAAAAAAH4E/5FLcOOVsmkAq2Xz23KfZhi6qm2_i8EYzwCLcB/s1600/notify%2Bvs%2Bnotifyall%2Bmethod%2Bin%2BJAva.jpg)</div>

这就是为什么notifyAll()是比notify()更安全的选择，因为它会给所有线程发送通知。如果某个线程不能继续执行，依然有其它线程来继续工作。查看[这里](http://javarevisited.blogspot.sg/2012/10/difference-between-notify-and-notifyall-java-example.html)以了解更多关于Java中notify和notifyAll方法的区别。

### 这段代码在Java的多线程环境中有什么问题？
{% codeblock lang:java %}
if(account.getBallance() >= withdrawl ){
  double ballance = account.getBallance() - withdrawl;
  account.setBallance(ballance);
}
{% endcodeblock %}

这段代码是受[Cay S. Horstmann的Core Java Volume 1](https://www.amazon.com/Core-Java-I--Fundamentals-10th/dp/0134177304?tag=javamysqlanta-20)中的银行例子启发而写出来的，这本书是最适合用来学习Java的书籍之一。很多情况下，**非原子操作**需要绑在一起执行。如果多个线程在执行这段代码，那很可能某个线程在检测完条件后进入等待状态，也就是帐号余额充足，但是当这个线程再次启动的时候，情况可能已经被另一个执行提现操作的线程产引发了变化，现在已经没有足够的余额来完成这次交易了。

<div align=center>[![avatar](https://1.bp.blogspot.com/-9zRVyLZIJkw/WJkUbcxbv6I/AAAAAAAAH3o/is25-d2wuccml2sjH8ucVYkd8Z1tGTcdACLcB/s320/Core%2BJava%2BVolume%2B1%2B10th%2BEdition.jpg)](https://www.amazon.com/Core-Java-I--Fundamentals-10th/dp/0134177304?tag=javamysqlanta-20)</div>

为了保证这段代码线程安全并且无错，你需要将条件检测和交易一起包裹在同步块中，就像下面这样：

{% codeblock lang:java %}
// member variable in class
Object accountLock = new Object();

sychronized(accountLock){
 if(account.getBallance() &gt; withdrawl ){
  double ballance = account.getBallance() - withdrawl;
  account.setBallance(ballance);
 }
}

{% endcodeblock %}

如果在使用Java 5并且知道如何使用java.util.concurrent.lock.Lock接口，那么你也可以使用一个[ReentrantLock](http://javarevisited.blogspot.sg/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)来保护这段代码。这也被称为关键区块，一段需要同一时间只被一个线程执行的代码。

### Java中的线程能在没有获取锁的情况下进入同步块吗？
这是不可能的。一个线程只有在获取到同步块的锁的情况下才能进入块中。synchronized关键字会在线程进入同步块之前获取锁并在退出之后释放锁。因为一个同步块只有一个锁，所以如果另一个线程正在使用这个锁，那么当前线程需要一直等待，直到锁可用。

这儿还有很问题，有些Java程序员使用不同的锁来保护同一资源，这是完全错误的。这就像在你的浴室开了2扇门，每扇门都有自己的锁和钥匙。你不会想要和其他人一起上厕所对吧？

所以，你必须使用相同的锁对象或者互斥来保护同一资源，如果你需要使用[多个锁](http://javarevisited.blogspot.sg/2014/05/double-checked-locking-on-singleton-in-java.html)，那么就使用多层次的锁，也就是说，你先打开房门然后打开厕所门，因此你同时需要房门和厕所门的钥匙来使用厕所。

<div align=center>[![avatar](https://4.bp.blogspot.com/-OXJh3HekfwY/WJkb7-v9yBI/AAAAAAAAH4M/PKLtDsRf3tsWfccrRAODSz6s1xbPiRjUQCLcB/s320/synchronized%2Bblock%2Bin%2BJava%2B2.jpg)](https://4.bp.blogspot.com/-OXJh3HekfwY/WJkb7-v9yBI/AAAAAAAAH4M/PKLtDsRf3tsWfccrRAODSz6s1xbPiRjUQCLcB/s1600/synchronized%2Bblock%2Bin%2BJava%2B2.jpg)</div>

### 线程调用call()方法后会发生什么？它会归还锁吗？
这是这个话题里最有意思的问题。我见过很多程序员在面试中让自己产生困惑，为什么第一个线程在调用wait()卡住后其它线程会获得锁。

其实，当一个线程发现条件不足以继续执行，它会决定调用wait()方法来等待执行，但它确实是会释放锁的。是的，这点很重要，线程不会继续执行，但是锁会释放，其它等等锁的线程就可以继续执行了。这也是sleep()和wait()方法的关键不同之处，它是用来将线程暂停的（[查看这里](http://javarevisited.blogspot.sg/2011/12/difference-between-wait-sleep-yield.html)）。

### 当其它线程调用了notifyAll()时处于等待中的线程（调用了wait()方法的线程）会发生什么？
当一个线程调用了notifyAll()或singalAll()方法，所有等待某个条件（一个与锁关联的条件并且notifyAll()是在持有锁的对象上被调用）的线程都会收到一个通知。现在它们已经从等待状态中退出了，但是它们想要继续执行的话依然需要线程调度器来分配CPU资源。当它们获得机会后，它们继续执行。查看[这里](http://www.java67.com/2016/04/10-points-about-wait-notify-and-notifyAll-in-java-multithreading.html)来了解更多关于Java中wait，notify和notifyAll方法的知识。

### 正在等待锁的线程和调用wait()方法后进入等待状态的线程有什么区别？
如果锁可用了，并且一个正在等待锁的线程获取到了锁，并且被分配了CPU资源，那么它会被激活（适合再次运行）。如果锁可用了，但是等待中的线程没有从其它线程接收到任何通知，那么它可能再也不会运行，比如，如果是用wait()方法进入等待的，会一直等到某个线程调用notify()或notifyAll()，如果是用Condition对象进入等待的，会一直等到调用signal()或者signalAll()。

<div align=center>[![avatar](https://2.bp.blogspot.com/-uJFSemzjcSM/WJkcOLNRSHI/AAAAAAAAH4Q/XcyXuO1UOx8qv8ZYJ1Hrc7vUGnbTg_6pgCLcB/s320/wait%2Bvs%2Byield%2Bvs%2Bsleep%2Bin%2BJava.png)](https://2.bp.blogspot.com/-uJFSemzjcSM/WJkcOLNRSHI/AAAAAAAAH4Q/XcyXuO1UOx8qv8ZYJ1Hrc7vUGnbTg_6pgCLcB/s320/wait%2Bvs%2Byield%2Bvs%2Bsleep%2Bin%2BJava.png)</div>

### 为什么应该在同步方法或同步块里调用wait()方法？
我已经对这个问题做了详细的解答，请阅读[这篇](http://javarevisited.blogspot.sg/2011/05/wait-notify-and-notifyall-in-java.html)文章来了解在非同步环境中调用wait和notify的风险和危险。

### 为什么应该在循环而不是if分支中检查等待条件？
一个有意思的并且很实用的问题。你需要在循环中检查等待条件，因为有可能在调用了notify()来唤醒线程后，等待条件依然满足。如果一个线程在被唤醒后不再次检查等待条件，那么它可以在接下来做出错误的操作。

这可能是由于许多原因造成的，比如超过一个线程正基于那个条件等待，而在第二个线程获取机会之前，第一个线程已经往后执行了，进而使得之前的唤醒操作无效了。 

比如，如果3个消费者都在等待，然后一个生产者将一个条目放入队列中并且通知了它们3个，那么第一个消费者将会取得这个条目而其它2个则需要继续等等。而这只有在循环中检查条件才有可能实现。

另外，这也是Joshua Bloch在[Effective Java](http://www.amazon.com/dp/0321356683/?tag=javamysqlanta-20)中建议的使用wait()和notify()的标准方式。

<div align=center>[![avatar](https://4.bp.blogspot.com/-5t9m4siY5CE/WJkNKwWjRDI/AAAAAAAAH3Y/n1SFWfOl1mQqxR6vZYmLWP5ruY7Q8Oh4gCLcB/s320/Effective%2BJava.jpg)](http://www.amazon.com/dp/0321356683/?tag=javamysqlanta-20)</div>

### 为什么在java.lang.Object类而不是Thread中定义notify方法？
简单的答案是因为java的Object同时也是一个监听器。它自带一个关联的锁，而wait()，notify()都与基于锁的等待有关，所以将它们定义在java.lang.Object上就说的过去了。如果想要更长的答案，看看[这篇](http://javarevisited.blogspot.com//2012/02/why-wait-notify-and-notifyall-is.html)文章。

以上就是所有关于一些**Java面试题中wait()和notify()，加锁，同步以及线程通讯的内容**。我知道，这些是想要理解和掌握都会很棘手的概念，所以我才会问这些棘手的问题。一旦你对回答这些问题感到很舒服，那么你对wait()和notify()的理解就会变成牢不可破。

坚持不断编写代码并思考如果同样的代码块在多个线程中运行的时候会发生什么。记住，线程可以在任意一行停止，而仅仅一行代码也可能是由多个指令组合而成比如++或!=，它们不是原子的。

我还建议至少通读一便[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)。它会填充你知识库中Java多线程、同步与并发之间的缝隙。

其它你可能感兴趣的**Java多线程面试题**
50个顶级Java线程面试题及答案（[列表](http://www.java67.com/2012/08/5-thread-interview-questions-answers-in.html)）
为经验丰富的程序员准备的12个顶级Java并发问题（[查看这里](http://java67.blogspot.sg/2012/08/5-thread-interview-questions-answers-in.html)）
投资银行的15个顶级多线程和并发问题（[查看这里](http://javarevisited.blogspot.com//2011/07/java-multi-threading-interview.html)）
过去5年内的133个核心Java面试题（[查看这里](https://javarevisited.blogspot.com/2015/10/133-java-interview-questions-answers-from-last-5-years.html)）
Java的volatile变量是如何工作的？（[回答](http://javarevisited.blogspot.sg/2011/06/volatile-keyword-java-example-tutorial.html)）
10个顶级Java并发及多线程最佳实践（[文章](http://javarevisited.blogspot.com/2015/05/top-10-java-multithreading-and.html)）
Java中start()和run()方法的区别？（[回答](http://javarevisited.blogspot.sg/2014/09/common-java-multi-threading-mistakes-1-run-vs-start.html)）

感谢你阅读本文。如果你喜欢这篇文章，那么请分享给你的朋友和同事。如果你有任何建议或反馈，请留下一条评论。另外，如果你觉得Java Concurrency in Practice已经不属性Java 8的时代了，那么请你在作出评判之前先读一读[这篇](http://javarevisited.blogspot.sg/2016/12/is-java-concurrency-in-practice-still-relevant-in-era-of-java8.html)文章。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)