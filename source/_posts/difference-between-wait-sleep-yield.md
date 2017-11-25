---
title: wait和yield的区别
date: 2017-11-25 11:17:34
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2011/12/difference-between-wait-sleep-yield.html

在**Java中wait和sleep的区别或者sleep和yeild的区别**是一个很流行的[核心Java面试题](http://javarevisited.blogspot.com/2015/10/133-java-interview-questions-answers-from-last-5-years.html)并会在[多线程面试](http://javarevisited.blogspot.com/2011/07/java-multi-threading-interview.html)时被问到。在Java中可以使用三种方法来暂停Java线程，sleep()和yield()方法定义在Thread类中，而wait()定义在Object类中，这正好也是另一个面试题。wait()和sleep()的关键区别是前者用于[线程通讯](http://javarevisited.blogspot.com/2013/12/inter-thread-communication-in-java-wait-notify-example.html)而后者用于将线程暂停一段时间。这个区别从事实上看更明显，当某个线程调用wait()方法的时候，它会释放它所持有的那个对象的监视器或对象锁，而当某个线程调用sleep()方法的时候，就算它持有监视器也不会释放。

回到yield()，它与wait()和sleep()有一点小区别，它只会释放它持有的CPU周期让其它线程有机会运行，尽管并不保证哪个线程会获得CPU周期。哪个线程获取CPU周期完全取决于线程调度器，甚至有可能调用yield()方法的那个线程会再次获得CPU周期。因此，依靠yield()方法并不可信，它仅仅只会基于最佳效果而已。

# Java中的wait与sleep与yield

在这篇教程中，我们将会学习Java的sleep是什么，Java sleep的重点以及Java中wait和sleep的区别。
## Java中wait和sleep的区别

wait与sleep的主要区别是wait()方法在线程等待时**释放取得的监视器**，而Thread.sleep()方法就算线程在等等状态也会**保持对象锁**或监视器。另外，Java的wait方法需要在同步方法或块中调用，而sleep()没有这样的需求。

另一个区别是**Thread.sleep()**方法是一个静态方法并**应用在当前线程**上，而[wait()](http://javarevisited.blogspot.com/2015/07/how-to-use-wait-notify-and-notifyall-in.html)则是一个具体的实例方法并且在某个其它线程在同一个对象上调用notify方法的时候被唤醒。另外，如果是sleep，睡眠的线程在醒来后直接进入Runnable状态，而等待状态的线程在被唤醒后首先要取得对象锁后才能进入Runnable状态。所以基于你的需求，如果你想[将线程暂停](http://java67.blogspot.sg/2015/06/how-to-pause-thread-in-java-using-sleep.html)一段时间那就使用sleep()方法，而如果你需要实现线程通讯则使用wait方法。

这里是Java中wait与sleep的一份区别清单：

1) wait只能从同步环境中调用而sleep可以在非同步环境调用。查看[为什么wait和notify需要在同步方法或块中调用](http://javarevisited.blogspot.com/2011/05/wait-notify-and-notifyall-in-java.html)以了解更多。

2) 等待状态的线程可以通过调用notify和notifyAll被唤醒，而睡眠状态的线程无法通过调用notify方法醒来。

3) wait一般通过条件来完成，线程在某个条件为真是进入等待，而sleep则直接将线程睡眠。

4) 线程在等待时释放该对象的对象锁，而sleep在睡眠时不释放。

5) wait()方法在某个对象上被调用，这个对象是被同步环境锁定的，而sleep是在Thread上调用的。查看Cay S. Horstmannr的[Core Java Volume 1第9版](http://aax-us-east.amazon-adsystem.com/x/c/QtVL5qq7ScwDYPJbZjVHTqoAAAFf8S3tIQEAAAFKAYz4eVM/http://www.amazon.com/Volume-II-Advanced-Features-Edition-Series/dp/013708160X/ref=as_at?creativeASIN=013708160X&linkCode=w61&imprToken=2xOj9Q647j.TTalgT80nFg&slotNum=0&tag=javamysqlanta-20)来了解更多关于如何在Java中使用wait()和notify()方法的内容。

<div align=center>[![avatar](https://4.bp.blogspot.com/-PJgmgfaEAu8/VvTEIOKEhDI/AAAAAAAAFUc/AmQWphpdTUU9MqlniBtnnWrnpb_BZZ1PQ/s320/Core%2BJava%2BVolume%2B1%2BHorstmann.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/QtVL5qq7ScwDYPJbZjVHTqoAAAFf8S3tIQEAAAFKAYz4eVM/http://www.amazon.com/Volume-II-Advanced-Features-Edition-Series/dp/013708160X/ref=as_at?creativeASIN=013708160X&linkCode=w61&imprToken=2xOj9Q647j.TTalgT80nFg&slotNum=1&tag=javamysqlanta-20)</div>

## Java中yield和sleep的区别

Java中yield和sleep的主要区别是yield()方法会临时**停止当前正在执行的线程**以使得其它等待中的优先级相同的线程有机会得以执行。如果没有其它等待中的线程或者所有等待中的线程的优先级都更低，那么当前线程会继续执行。yielded线程什么时候有机会执行由线程调度器决定，它们的行为依赖各家供应商。yield方法不会保证当前线程暂停或停止，但是它会保证CPU周期被当前线程放弃，这就是在Java中调用Thread.yield()的结果。查看[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QtVL5qq7ScwDYPJbZjVHTqoAAAFf8S3tIQEAAAFKAYz4eVM/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=2xOj9Q647j.TTalgT80nFg&slotNum=2&tag=javamysqlanta-20)以及了解更多。

Java的sleep方法拥有2个变体，其中一个取毫秒作为睡眠时间，而另一个同时取毫秒和纳秒作为睡眠持续时间。

{% codeblock lang:java %}
sleep(long millis)
or
sleep(long millis,int nanos)
{% endcodeblock %}

使**当前运行中的线程睡眠**毫秒加上纳秒的时间长度。

这里有一个不错的图例，展示了调用wait()、sleep()和yield()方法时线程是如何过渡到其它状态的。

<div align=center>[![avatar](https://3.bp.blogspot.com/-SNJgCtjH9HI/VvTEb0u9RXI/AAAAAAAAFUg/Z_uW1Z8j8SEg3A1GigzIZY2UNh8_QH6-A/s1600/Sleep%2Bvs%2Bwait%2Bvs%2Byield%2Bmethod%2Bjava.gif)](https://3.bp.blogspot.com/-SNJgCtjH9HI/VvTEb0u9RXI/AAAAAAAAFUg/Z_uW1Z8j8SEg3A1GigzIZY2UNh8_QH6-A/s640/Sleep%2Bvs%2Bwait%2Bvs%2Byield%2Bmethod%2Bjava.gif)</div>

# Java的Thread.sleep()方法示例
这里是一份简单的sleep [Java线程](http://javarevisited.blogspot.com/2011/02/how-to-implement-thread-in-java.html)的示例代码。在示例中，我们将主线程睡眠了1秒。

{% codeblock lang:java %}
/*
 * Example of Thread Sleep method in Java
 */
public class SleepTest {
      
       public static void main(String... args){
              System.out.println(Thread.currentThread().getName() + " is going to sleep for 1 Second");
              try {
                     Thread.currentThread().sleep(1000);
              } catch (InterruptedException e) {
                     // TODO Auto-generated catch block
                     e.printStackTrace();
              }
              System.out.println("Main Thread is woken now");
       }

}

Output:
main is going to sleep for 1 Second
Main Thread is woken now
{% endcodeblock %}

## Java中Thread的sleep()方法的10点
我在下面列举了一些重要的并且值得记住的[Java类](http://javarevisited.blogspot.com/2011/10/class-in-java-programming-general.html)Thread的sleep()方法的小点

1) Thread.sleep()方法用于**暂停执行，放弃CPU周期并将其归还给线程**调度器。

2) Thread.sleep()方法是一个[静态方法](http://javarevisited.blogspot.com/2011/11/static-keyword-method-variable-java.html)，并且永远只将当前线程置入睡眠。

3) Jav中Thread类的sleep方法a拥有2个变体，一个取毫秒传为睡眠持续时间，另一个拥有2个参数的方法其中一个参数是毫秒另一个参数是纳秒。

4) 不像[Java的wait()方法](http://javarevisited.blogspot.com/2011/05/wait-notify-and-notifyall-in-java.html)，Thread类的sleep()方法**不会放弃它取得的对象锁**。

5) 如果Java中的另一个线程中断了一个睡眠线程，sleep()方法会**抛出Interrupted异常**。

6) 使用Java的sleep()不能保证当线程醒来后马上获得CPU周期，反而是进入Runnable状态并和其它线程竞争CPU周期。

7) 有一个对于Java sleep方法的**误解**，那就是调用t.sleep()会将线程“t”置入睡眠状态，但是这是不正确的因为Thread.sleep是一个静态方法，它总是会将当前线程置入睡眠状态而不是线程“t”。

以上就是所有关于**Java sleep方法**的内容。我们已经看见了**Java中sleep与wait**以及**sleep与yield的区别**。总之，切记sleep()和yield()2者都是操作的当前线程。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)

### 其它你可能喜欢的Java教程:
[Java中ArrayList与Vector的区别](http://javarevisited.blogspot.com/2011/09/difference-vector-vs-arraylist-in-java.html)
[Java的substring是如何工作的](http://javarevisited.blogspot.com/2011/10/how-substring-in-java-works.html)
[Java中SynchronizedCollection与ConcurrentCollection的区别](http://javarevisited.blogspot.com/2011/04/difference-between-concurrenthashmap.html)
[为什么Java的main方法是静态的](http://javarevisited.blogspot.com/2011/12/main-public-static-java-void-method-why.html)
[Java的volatile关键字是如何工作的](http://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html)
[Java中ComparatorComparable的区别](http://javarevisited.blogspot.com/2011/06/comparator-and-comparable-in-java.html)
[Java中ClassNotFoundExceptionNoClassDefFoundError的区别](http://javarevisited.blogspot.com/2011/07/classnotfoundexception-vs.html)