---
title: '如何在java中使用wait, notify和notifyAll - 生产者消费者例子'
date: 2017-11-10 13:21:21
tags: 多线程
---
翻译自：http://javarevisited.blogspot.sg/2015/07/how-to-use-wait-notify-and-notifyall-in.html

在java中可以使用wait, nofity和notifyAll进行线程通讯。比如，如果你的程序拥有2个线程，如生产者和消费者，那么生产者就可以告诉消费者队列中已经有了条目，它可以进行消费了。同样的，消费者可以告诉生产者队列已空，它可以进行生产了，其正是消费者消费所产生的结果。线程可以根据某些条件使用wait方法暂停执行而不做任何事情。比如，在[生产者消费者问题](http://java67.blogspot.sg/2012/12/producer-consumer-problem-with-wait-and-notify-example.html)中，生产者应该在队列已满时暂停执行，而消费者应该在队列为空时暂停执行。

如果某些线程在等待某些条件满足，那你可以使用notify和notifyAll来通知它们这些条件已经产生变化，它们可以被继续执行了。

notify和notifyAll方法都发送通知，但是notify只会给一个等待中的线程发送通知，不保证哪个线程会收到，而notifyAll会给所以线程发送通知。

所以如果只有一个线程正在等待对象锁，也就是所谓的监听器，那么notify和notifyAll都会给它发送通知。如果[多个线程](http://javarevisited.blogspot.com/2013/02/how-to-join-multiple-threads-in-java-example-tutorial.html)正在等待监听器，那么notify只会通知某个幸运的线程而其它线程是不会收到任何通知的，而notifyAll则会通知所有线程。

在这篇Java多线程教程中你将学会**如何在Java中使用wait, notify和notifyAll**来实现线程间沟通以解决生产者消费者问题。

顺带一提，如果你真的想掌握并发和多线程，那么我强烈建议你去读一下Brian Goetz的[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)一书，不读一读这本书你的Java多线程之旅将会不完整。它或许是最适合推荐给Java开发者的书之一。

<div align=center>[![avatar](https://2.bp.blogspot.com/-KcNRWpzatyQ/VwPZPsFtjoI/AAAAAAAAFoY/Fzwv6eMEtroKnDlvL45wvgOX4qNUZ_8YA/s320/Java%2BConcurrency%2Bin%2BPractice.jpg)](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)</div>

## 如何在代码中使用wait和notify

尽管wait和notify都是十分基础的概念并且都定义在Object类中，但出人意料地，要使用它们编码并不容易。你可以通过在面试中让候选人手动编码解决生产者消费者问题来测试这点。

我很确定多数人会卡住并犯下错误，比如在错误的地方使用同步，在错误的对象上调用wait方法或者没有遵循标准语义。老实的说，不正规编码的人员对它很容易产生困惑。

第一个困惑产生自这样一个事实，如何调用wait方法？由于[wait方法并没有定义在Thread类中](http://javarevisited.blogspot.sg/2012/02/why-wait-notify-and-notifyall-is.html)，你不能简单的调用Thread.wait()，这不会起作用，但因为很多Java开发者习惯调用Thread.sleep()，所以他们会尝试同样的事情然后卡住。

你需要在线程间共享的对象上调用wait方法，它在在生产者-消费者问题中是队列对象，在生产者和消费者线程间共享。

第二个困惑来自下面的事实，也就是wait方法需要在同步块还是方法中调用？如果你使用同步块，哪个对象应该被放入块中？这应该是同样的对象，你想要获取它的对象锁，也就是线程间共享的对象。在我们例子中是队列。

<div align=center>[![avatar](https://4.bp.blogspot.com/-REiHbzOXAwM/VZPzn9IOM5I/AAAAAAAADOw/6oX4j4aBUno/s400/wait%2Bmethod%2Bin%2BJava%2B2.jpg)](https://4.bp.blogspot.com/-REiHbzOXAwM/VZPzn9IOM5I/AAAAAAAADOw/6oX4j4aBUno/s400/wait%2Bmethod%2Bin%2BJava%2B2.jpg)</div>

## 永远只在循环中调用wait和notify而不是if块中

一旦你知道你需要在同步上下文中并在共享对象上调用wait，接下来要做就是避免一些Java开发者犯的错误，在条件判断块中调用wait()而不是循环中。

由于你在条件分支中调用wait，比如生产者应该在队列已满时调用wait()，第一本能让你使用if块，但是在if块中调用wait()会导致产生狡猾的bug，因为就算条件没有发生变化线程也可能不合逻辑地被唤醒。

如果你使用循环唤醒线程后不再次检查条件，你会执行错误的动作，可能会产生问题，比如在一个已满的队列上插入条目或试图消费一个空队列。那就是为什么你应该在循环而不是条件判断中调用wait和nofity方法。

另外我建议读读[Effective Java](https://www.amazon.com/dp/0321356683/?tag=javamysqlanta-20)的类似章节，这可能是如何正确调用wait和notify方法的最佳引用内容。

<div align=center>[![avatar](https://2.bp.blogspot.com/-BFXzqYd013M/VwPafgHJ28I/AAAAAAAAFok/NtIG5S8sns4dxjqaRM0n_Fh-dVpTI9qew/s320/Effective%2BJava.jpg)](http://www.amazon.com/dp/0321356683/?tag=javamysqlanta-20)</div>

基于以上知识下面是如果在Java中调用wait和notify方法的标准模板代码或语义。

{% codeblock lang:java %}
// The standard idiom for calling the wait method in Java
synchronized (sharedObject) {
   while (condition) {
      sharedObject.wait(); // (Releases lock, and reacquires on wakeup)
   }
   ... // do action based upon condition e.g. take or put into queue
}
{% endcodeblock %}

就如我所建议的，你应该就远只在循环中调用wait方法。这个循环是用来在等待之前和之后判断条件的。如果条件依然满足，并且[notify（或notifyAll）方法](http://java67.blogspot.sg/2013/03/difference-between-wait-vs-notify-vs-notifyAll-java-thread.html)在调用wait()方法之前已经被调用，那么就不能保证线程在wait之后一定会被唤醒，也就有可能导致死锁的产生。

## Java中使用wait(), notify()和notifyAll()的示例

这里是我们用于展示在java中如何使用wait和notify的示例程序。在这个程序中，我们使用了上文讨论过的标准语义来在java中调用wait()，notify()和notifyAll()。

在这个程序中，我们有2个线程分别叫PRODUCER和CONSUMER，并且基于继承了Thread类的Producer和Consumer类来实现。生产者和消费者各自的逻辑应该写在各自的run()方法中。

Main thread starts both producer and consumer threads and also create an object of LinkedList class to share as Queue between them. If you don't know LinkedList also implements Queue interface in Java.
Main线程同时启动了生产者和消费者线程，并且还创建了一个在2个线程间共享的LinkedList类对象。如果你不知道[在java中LinkList也实现了Queue接口](http://java67.blogspot.sg/2012/12/difference-between-arraylist-vs-LinkedList-java.html)。

生产者在一个无限循环中运行，并且只要队列未满，就会一直插入随机整数到队列中。我们在while(queue.size == maxSize)中检查这个条件，记住在检查这个条件之前我们会在队列对象上做同步操作，以免其他线程在执行这个检查时修改队列。

如果Queue已满，那么PRODUCER线程会一直等待，直到CONSUMER线程消费一个条目让队列出现空位并调用notify()方法来通知PRODUCER线程。**wait()和notify()都会在共享对象上调用，而在我们的例子中是队列对象。**

{% codeblock lang:java %}
import java.util.LinkedList;
import java.util.Queue;
import java.util.Random;

/**
 * Simple Java program to demonstrate How to use wait, notify and notifyAll()
 * method in Java by solving producer consumer problem.
 * 
 * @author Javin Paul
 */
public class ProducerConsumerInJava {

    public static void main(String args[]) {
        System.out.println("How to use wait and notify method in Java");
        System.out.println("Solving Producer Consumper Problem");
        
        Queue<Integer> buffer = new LinkedList<>();
        int maxSize = 10;
        
        Thread producer = new Producer(buffer, maxSize, "PRODUCER");
        Thread consumer = new Consumer(buffer, maxSize, "CONSUMER");
        
        producer.start();
        consumer.start();
        

    }

}

/**
 * Producer Thread will keep producing values for Consumer
 * to consumer. It will use wait() method when Queue is full
 * and use notify() method to send notification to Consumer
 * Thread.
 * 
 * @author WINDOWS 8
 *
 */
class Producer extends Thread {
    private Queue<Integer> queue;
    private int maxSize;
    
    public Producer(Queue<Integer> queue, int maxSize, String name){
        super(name);
        this.queue = queue;
        this.maxSize = maxSize;
    }
    
    @Override
    public void run() {
        while (true) {
            synchronized (queue) {
                while (queue.size() == maxSize) {
                    try {
                        System.out .println("Queue is full, "
                                + "Producer thread waiting for "
                                + "consumer to take something from queue");
                        queue.wait();
                    } catch (Exception ex) {
                        ex.printStackTrace();
                    }
                }

                Random random = new Random();
                int i = random.nextInt();
                System.out.println("Producing value : " + i);
                queue.add(i);
                queue.notifyAll();
            }

        }
    }
}

/**
 * Consumer Thread will consumer values form shared queue.
 * It will also use wait() method to wait if queue is
 * empty. It will also use notify method to send 
 * notification to producer thread after consuming values
 * from queue.
 * 
 * @author WINDOWS 8
 *
 */
class Consumer extends Thread {
    private Queue<Integer> queue;
    private int maxSize;
    
    public Consumer(Queue<Integer> queue, int maxSize, String name){
        super(name);
        this.queue = queue;
        this.maxSize = maxSize;
    }
    
    @Override
    public void run() {
        while (true) {
            synchronized (queue) {
                while (queue.isEmpty()) {
                    System.out.println("Queue is empty,"
                            + "Consumer thread is waiting"
                            + " for producer thread to put something in queue");
                    try {
                        queue.wait();
                    } catch (Exception ex) {
                        ex.printStackTrace();
                    }

                }
                System.out.println("Consuming value : " + queue.remove());
                queue.notifyAll();
            }

        }
    }
}

Output
How to use wait and notify method in Java
Solving Producer Consumper Problem
Queue is empty,Consumer thread is waiting for producer thread to put something in queue
Producing value : -1692411980
Producing value : 285310787
Producing value : -1045894970
Producing value : 2140997307
Producing value : 1379699468
Producing value : 912077154
Producing value : -1635438928
Producing value : -500696499
Producing value : -1985700664
Producing value : 961945684
Queue is full, Producer thread waiting for consumer to take something from queue
Consuming value : -1692411980
Consuming value : 285310787
Consuming value : -1045894970
Consuming value : 2140997307
Consuming value : 1379699468
Consuming value : 912077154
Consuming value : -1635438928
Consuming value : -500696499
Consuming value : -1985700664
Consuming value : 961945684
Queue is empty,Consumer thread is waiting for producer thread to put something in queue
Producing value : 1182138498
{% endcodeblock %}

为了更好的理解这个程序，我建议你高度它而不是运行。一旦你以高度程序启动程序，它会停止在PRODUCER或CONSUMER线程二者之一中，取决于那个线程被调度器给予CPU时间。

由于2个线程的wait条件最终都会被满足，现在你只能运行一下看看它做了什么，最有可能打印出上面的输出。你还可以[用Eclipse的Step Into和Step Over按钮](http://javarevisited.blogspot.sg/2011/07/java-debugging-tutorial-example-tips.html)一步步运行程序来更好的理解它。

## 使用wait(), notify()和notifyAll()方法需要注意的地方

1. 在Java中可以使用wait()和notify()方法来实现线程间通讯。不只是一个或两个，这些方法可以用来在多个线程中与彼此通讯。
2. 总是在同步方法或同步块中调用wait(), nofity()和notifyAll()方法，否则JVM会抛出IllegalMonitorStateException。
3. 总是在循环中调用wait和notify方法，永远不在if()块中调用，因为循环在睡眠之前和之后都会检查等待条件并处理通知，甚至等待条件未发生变化也一样。
4. 总是在共享对象上调用wait比如这个例子中的队列对象。
5. 基于[这篇](http://javarevisited.blogspot.sg/2012/10/difference-between-notify-and-notifyall-java-example.html)文章给出的原因优先选择notifyAll()而不是notify()方法。

<div align=center>[![avatar](https://3.bp.blogspot.com/-icSDvcAustg/VZP05Fy5RUI/AAAAAAAADO8/G4zbwhOMiy4/s640/Producer%2BConsumer%2Bpatern%2Busing%2Bwait%2Bnotify%2Bjava.jpg)](http://3.bp.blogspot.com/-icSDvcAustg/VZP05Fy5RUI/AAAAAAAADO8/G4zbwhOMiy4/s1600/Producer%2BConsumer%2Bpatern%2Busing%2Bwait%2Bnotify%2Bjava.jpg)</div>

这些就是所有**java中该如何使用wait, notify和notifyAll()方法**。你应该只在清楚知道自己在做什么的时候使用wait和notify来做[inter-thread communication in Java](http://javarevisited.blogspot.sg/2013/12/inter-thread-communication-in-java-wait-notify-example.html)，如果不是的话，先尝试已经存在的大量并发工具库来解决问题。

比如，如果你想要实现生产者-消费者模式，那么你可以使用BlockingQueue，它会同时为你管理线程安全和流控制，如果你需要你的线程在继续执行之前应该等待其它线程，你可以使用[CycliBarrier or CountDownLatch](http://java67.blogspot.sg/2015/06/how-to-use-cyclicbarrier-in-java.html)，或者如果你需要保护资源你可以使用Semaphore。