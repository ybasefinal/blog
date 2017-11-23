---
title: Java中notify与notifyAll之间有何区别的示例
date: 2017-11-23 18:05:36
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2012/10/difference-between-notify-and-notifyall-java-example.html

### Java notify与notifyAll 
notify和notifyAll方法有什么区别是一个[很狡猾的Java面试题](http://java67.blogspot.sg/2012/09/top-10-tricky-java-interview-questions-answers.html)，这个问题虽然很容易回答，但是一旦面试官询问后续问题，你可能要么产生困惑要么不能提供明确的并且切中要害的回答。notify和notifyAll的主要区别是notify方法只会通知一个[线程](http://javarevisited.blogspot.sg/2011/02/how-to-implement-thread-in-java.html)而notifyAll方法则通知所有正在等等监视器或对象锁的线程。随便说一下，你在其它地方都会看到这样的解释，不过坦白说，这句话虽然正确但却不够完整，而且也很难只通过这句话来理解notify和notifyAll的区别。大量问题浮现脑海中，如

如果我使用notify()，那么哪个线程会被通知？
我怎么知道有多少等待线程的时候，我才能使用notifyAll()呢？
怎么调用notify()？
这些在等待被通知的线程是什么等等？

事实上，如果不将notify和notifyAll与Java的wait方法放在一起讨论，就不会有完整的结果，基于这个原因我提及了之前写的一篇文章[为什么wait和notify必须在同步环境中调用](http://javarevisited.blogspot.sg/2011/05/wait-notify-and-notifyall-in-java.html)。为了获得这些问题的答案并且理解notify与notifyAll之间的区别，我将会使用一个简单的用wait和notify代码写的Java线程示例：



# Java中notify与notifyAll的区别

Java提供了notify和notifyAll这2个方法来唤醒正在等待某个条件的线程，你可以随意的使用它们，但是这里Java的notify和notifyAll之间有一个微妙的区别，这也让它成为了[Java面试中最流行的多线程面试题](http://javarevisited.blogspot.sg/2011/07/java-multi-threading-interview.html)。当你调用notify的时候只有一个等待线程会被唤醒而且不保证被唤醒的具体是哪一个，这取决于线程调度器。而如果你调用notifyAll方法，则所有正在等待那个对象锁的线程都会被唤醒，但是所有被唤醒的线程在执行余下代码之前又会再一次竞争对象锁，这就是为什么必须要在循环中调用wait的原因了，因为如果多个线程被唤醒，第一个执行的线程可能会重置等待条件，而后果就是所有剩下的线程都需要重新[等待](http://javarevisited.blogspot.sg/2011/12/difference-between-wait-sleep-yield.html)。所以notify和notifyAll的关键区别就是notify()会导致只有一个线程被唤醒而notifyAll方法则会唤醒所有线程。


### 什么时候在Java中使用notify和notifyAll？
如果你过了上一个问题，Java中notifyAll与notify的区别，那么这就会是下一个问题了。如果你理解notify与notifyAll之间的对比，那么你可以通过应用小常识来回答这个问题。如果所有线程都在等待同一个条件，并且同一时间只能有一个线程能在条件为真时受益，那么你可以越过notifyAll使用notify。这种情况下，因为不会唤醒所有等待线程，所以notify是比notifyAll更优化的代码，因为我们知道只有一个线程会受益而其它线程会再次等待，所以调用notifyAll方法只是单纯的浪费CPU周期而已。尽管这看起来很合理，但是针对它还有一个警告，那就是关键通知有可能会被意想不到的接受者吞没。使用notifyAll我们可以确保所有接受者都会收到通知。Josh Bloch在他的[Effective Java](http://aax-us-east.amazon-adsystem.com/x/c/Qvs24aMUpbCUmEJloJi8KMkAAAFf6FXnXgEAAAFKAXiw03s/http://www.amazon.com/dp/0321356683/ref=as_at?creativeASIN=0321356683&linkCode=w61&imprToken=4rAncPn5plbb6obH.z875w&slotNum=0&tag=javamysqlanta-20)中详细解释了这点，如果你还没有读过这本书我高度建议你去读一下。另外还可以尝试Concurrency Practice in Java and Java Thread，它讨论的是[wait和notify方法](http://javarevisited.blogspot.sg/2012/02/why-wait-notify-and-notifyall-is.html)的细节。

<div align=center>[![avatar](https://2.bp.blogspot.com/-D4QCA4JeQw4/VxsacB5TQ3I/AAAAAAAAFvM/r5qGvVmyYDstfWDZfsqDn8AHLWXkBYYoQCLcB/s320/Effective%2BJava.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/Qvs24aMUpbCUmEJloJi8KMkAAAFf6FXnXgEAAAFKAXiw03s/http://www.amazon.com/dp/0321356683/ref=as_at?creativeASIN=0321356683&linkCode=w61&imprToken=4rAncPn5plbb6obH.z875w&slotNum=1&tag=javamysqlanta-20)</div>

## Java中使用notify和notifyAll方法的示例

我已经组装了一个例子，来展示在Java中调用nofityAll的时候如何唤醒所有线程，调用notify的时候如何只唤醒一个线程。在这个例子中如果boolean变量go为false，三个线程会等待，记住boolean go是一个[volatile变量](http://javarevisited.blogspot.sg/2011/06/volatile-keyword-java-example-tutorial.html)，所以所有线程都可以看到它的更新。最初WT1，WT2，WT3三个线程都会等待，因为变量go是false的，然后一个叫NT1的线程会将go设值为true，并调用notifyAll方法通知所有线程，或者调用notify()方法只通知其中某一个线程。如果调用的是notify()，那么不能确保哪个线程会被唤醒，你可以通过多次运行这个Java程序来验证这点。如果使用notifyAll，所有线程都会被唤醒，但是它们会竞争监视器或对象锁，然后第一个获得锁的线程完成它的执行并将go的值修改为了false，进而导致其它2个线程再次等待。在这个程序的结尾，你将会拥有2个等待状态线程和2个包括通知线程在内的完成状态线程。这个程序不会终止，因为另外2个线程依然在等待中，而它们也不是[后台进程](http://javarevisited.blogspot.sg/2012/03/what-is-daemon-thread-in-java-and.html)。这个notify和notifyAll示例的意图是为了为你展示notify和nofityAll方法在Java中是如果工作的。

### notify和notifyAll的代码示例
这里是如何在Java中使用notify和notifyAll的完整示例。我们已经解释过什么时候使用notify和notifyAll方法，而这个例子会阐明在Java中调用notify和notifyAll方法产生的影响。

{% codeblock lang:java %}
import java.util.logging.Level;
import java.util.logging.Logger;

/**
 * Java program to demonstrate How to use notify and notifyAll method in Java and
 * How to notify and notifyAll method notifies thread, which thread gets woke up etc.
 */
public class NotificationTest {

    private volatile boolean go = false;

    public static void main(String args[]) throws InterruptedException {
        final NotificationTest test = new NotificationTest();
      
        Runnable waitTask = new Runnable(){
      
            @Override
            public void run(){
                try {
                    test.shouldGo();
                } catch (InterruptedException ex) {
                    Logger.getLogger(NotificationTest.class.getName()).
                           log(Level.SEVERE, null, ex);
                }
                System.out.println(Thread.currentThread() + " finished Execution");
            }
        };
      
        Runnable notifyTask = new Runnable(){
      
            @Override
            public void run(){
                test.go();
                System.out.println(Thread.currentThread() + " finished Execution");
            }
        };
      
        Thread t1 = new Thread(waitTask, "WT1"); //will wait
        Thread t2 = new Thread(waitTask, "WT2"); //will wait
        Thread t3 = new Thread(waitTask, "WT3"); //will wait
        Thread t4 = new Thread(notifyTask,"NT1"); //will notify
      
        //starting all waiting thread
        t1.start();
        t2.start();
        t3.start();
      
        //pause to ensure all waiting thread started successfully
        Thread.sleep(200);
      
        //starting notifying thread
        t4.start();
      
    }
    /*
     * wait and notify can only be called from synchronized method or bock
     */
    private synchronized void shouldGo() throws InterruptedException {
        while(go != true){
            System.out.println(Thread.currentThread()
                         + " is going to wait on this object");
            wait(); //release lock and reacquires on wakeup
            System.out.println(Thread.currentThread() + " is woken up");
        }
        go = false; //resetting condition
    }
  
    /*
     * both shouldGo() and go() are locked on current object referenced by "this" keyword
     */
    private synchronized void go() {
        while (go == false){
            System.out.println(Thread.currentThread()
            + " is going to notify all or one thread waiting on this object");

            go = true; //making condition true for waiting thread
            //notify(); // only one out of three waiting thread WT1, WT2,WT3 will woke up
            notifyAll(); // all waiting thread  WT1, WT2,WT3 will woke up
        }
      
    }
  
}

Output in case of using notify
Thread[WT1,5,main] is going to wait on this object
Thread[WT3,5,main] is going to wait on this object
Thread[WT2,5,main] is going to wait on this object
Thread[NT1,5,main] is going to notify all or one thread waiting on this object
Thread[WT1,5,main] is woken up
Thread[NT1,5,main] finished Execution
Thread[WT1,5,main] finished Execution

Output in case of calling notifyAll
Thread[WT1,5,main] is going to wait on this object
Thread[WT3,5,main] is going to wait on this object
Thread[WT2,5,main] is going to wait on this object
Thread[NT1,5,main] is going to notify all or one thread waiting on this object
Thread[WT2,5,main] is woken up
Thread[NT1,5,main] finished Execution
Thread[WT3,5,main] is woken up
Thread[WT3,5,main] is going to wait on this object
Thread[WT2,5,main] finished Execution
Thread[WT1,5,main] is woken up
Thread[WT1,5,main] is going to wait on this object
{% endcodeblock %}

我强烈推荐你运行一下这个Java程序，弄懂它的输出，尝试理解它。实践出真理，如果你看到任何不一致的地方，请告知我们或者尝试改正它。和[死锁](http://javarevisited.blogspot.sg/2010/10/what-is-deadlock-in-java-how-to-fix-it.html)，[竞态条件](http://javarevisited.blogspot.sg/2012/02/what-is-race-condition-in.html)，和[线程安全](http://javarevisited.blogspot.sg/2012/01/how-to-write-thread-safe-code-in-java.html)一样，线程通讯也是Java并发编程基础的一部分。

### 总结
简而言之，这里是我们在这篇教程开端提出的notify和notifyAll面试题的答案：

**如果我使用notify()，那么哪个线程会被通知？**
没有保证，线程调度器会随机从一个等待某个监视器的线程池中挑选一个线程。只有一个线程会收到通知这点是受保证的。

**我怎么知道有多少等待线程的时候，我才能使用notifyAll()呢？**
这取决于你的程序逻辑，在编程里你需要想一想这块代码是否可以被多线程运行。一个可以用来理解线程通讯的好例子是通过用[Java实现生产者-消费者模式](http://javarevisited.blogspot.sg/2012/02/producer-consumer-design-pattern-with.html)。

**怎么调用notify()？**
wait()和notify()方法只能在[同步方法或块](http://javarevisited.blogspot.sg/2011/04/synchronization-in-java-synchronized.html)中调用，你需要在正在被其它线程等待的对象上调用notify方法。

**这些在等待被通知的线程是什么等等？**
线程等待某些条件满足，比如生产者-消费者问题中，生产者线程在队列已满时等待而消费者线程在队列为空时等待。由于多个线程都与一个共享资源有交互，它们之间通过wait和notify方法来与彼此交互。

以上就是所有关于Java中notify和notifyAll方法有什么区别和什么时候在Java中使用notify或notifyAll的内容。现在你应当能够在理解并在你自己的Java程序中使用notify和notifyAll方法做线程通讯了。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)