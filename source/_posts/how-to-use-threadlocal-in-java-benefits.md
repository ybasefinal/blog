---
title: Java中的ThreadLocal - 示例程序及教程
date: 2017-11-12 13:30:31
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2012/05/how-to-use-threadlocal-in-java-benefits.html

ThreadLocal是Java中除了编写不可变类之外另外一种实现[线程安全](http://javarevisited.blogspot.com/2012/01/how-to-write-thread-safe-code-in-java.html)的方法。如果你编写过Java的多线程或并发代码，那你肯定对同步或锁的消耗很熟悉，它们会影响程序的伸缩性，但是除了同步你没办法在多个线程之间共享对象。ThreadLocal是Java中另一种实现线程安全的方式，它的出发点不是同步需求，相反它在每个线程中提供一个明确地的复本来消除共享。由于对象不需要共享，同步也就不再需要了，进而实现提升程序的伸缩性及性能的目的。在这篇ThreadLocal教程中我们会解释Java中ThreadLocal的重点部分，什么时候使用它及一个简单的ThreadLocal Java程序。

## 什么时候在Java中使用ThreadLocal

很多java开发者对什么地方可以使用ThreadLocal有疑问，甚至有些人对ThreadLocal变量带来的好处有争义，但是ThreadLocal确实拥有一些适用场景，所以它被加入到了Java平台库中。但我同意只要你不做并发编程的话，你基本上用不上ThreadLocal。下面是Java中ThreadLocal几种广为人知的用处：

1)ThreadLocal极适合作为线程单例类或线程上下文如事务id。

2) 你可以把任何非线程安全的对象包裹在ThreadLocal内，它立马就变成了线程安全了，因为ThreadLocal的作用就是线程安全。一个ThreadLocal的经典例子是共享SimpleDateFormat。因为[SimpleDateFormat是非线程安全的](http://javarevisited.blogspot.com/2012/03/simpledateformat-in-java-is-not-thread.html)，保持一个全局formatter可能不会起作用，但是换成线程内formatter就没问题了。

3) ThreadLocal提供了一种另外的方式来继承Thread。如果你想维持或传递一个方法的信息到另一个方法上，你可以使用ThreadLocal。这会巨大的提升灵活性，因为你不需要修改任何方法。

从基本上来说ThreadLocal提供了线程封闭，它是[本地变量](http://javarevisited.blogspot.com/2012/02/difference-between-instance-class-and.html)的扩展。本地变量只能在定义它的块内可见，而ThreadLocal则只在当前线程内可见。没有任何2个线程能访问彼此的ThreadLocal变量。ThreadLocal一个真实存在的例子是[J2EE应用服务器](http://javarevisited.blogspot.com/2012/05/5-difference-between-application-server.html)，其使用ThreadLocal变量来跟踪事务和安全上下文。 将数据库连接之类的重度共享对象封装为ThreadLocal很有意义，这避免了过度创建对象及使用全局共享时的锁消耗。

## Java ThreadLocal 示例 – 代码

{% codeblock lang:java %}
import java.io.IOException;
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 *
 * @author
 */
public class ThreadLocalTest {

    public static void main(String args[]) throws IOException {
        Thread t1 = new Thread(new Task());   
        Thread t2 = new Thread( new Task());
      
        t1.start();
        t2.start();       
      
    }
    
    /*
     * Thread safe format method because every thread will use its own DateFormat
     */
    public static String threadSafeFormat(Date date){
        DateFormat formatter = PerThreadFormatter.getDateFormatter();
        return formatter.format(date);
    }
    
}


/*
 * Thread Safe implementation of SimpleDateFormat
 * Each Thread will get its own instance of SimpleDateFormat which will not be shared between other threads. *
 */
class PerThreadFormatter {

    private static final ThreadLocal<SimpleDateFormat> dateFormatHolder = new ThreadLocal<SimpleDateFormat>() {

        /*
         * initialValue() is called
         */
        @Override
        protected SimpleDateFormat initialValue() {
            System.out.println("Creating SimpleDateFormat for Thread : " + Thread.currentThread().getName());
            return new SimpleDateFormat("dd/MM/yyyy");
        }
    };

    /*
     * Every time there is a call for DateFormat, ThreadLocal will return calling
     * Thread's copy of SimpleDateFormat
     */
    public static DateFormat getDateFormatter() {
        return dateFormatHolder.get();
    }
}

class Task implements Runnable{
    
    @Override
    public void run() {
        for(int i=0; i<2; i++){
            System.out.println("Thread: " + Thread.currentThread().getName() + " Formatted Date: " + ThreadLocalTest.threadSafeFormat(new Date()) );
        }       
    }
}

Output:
Creating SimpleDateFormat for Thread : Thread-0
Creating SimpleDateFormat for Thread : Thread-1
Thread: Thread-1 Formatted Date: 30/05/2012
Thread: Thread-1 Formatted Date: 30/05/2012
Thread: Thread-0 Formatted Date: 30/05/2012
Thread: Thread-0 Formatted Date: 30/05/2012
{% endcodeblock %}

如果你看过上面程序的输出，你会发现当不同的线程调用ThreadLocal类的getFormatter()方法时，它调用的实际上是ThreadLocal的initialValue()方法，这会为该线程创建一个独有的SimpleDateFormat实例。因为SimpleDateFormat没有在线程间共享，并且本质上的讲只属于那个创建它的线程，所以threadSafFormat()方法完全是线程安全的。

## Java ThreadLocal类的重点

1. Java的ThreadLocal是在JDK 1.2引进的，但是后来在JDK 1.4时变成了泛型类以支持类型安全。

2. ThreadLocal可以与Thread scope关联起来，所有由当前线程运行的代码都可以访问ThreadLocal变量，但是其他线程不行。

3. 每个线程持有一个独有的ThreadLocal变量复本，当线程正常或异常运行完成或终止时gc会将其回收，但需要这些ThreadLocal变量不持有其他有效引用。

4. Java中ThreadLocal变量通常是类的私有静态属性，并在线程中维护其状态。

我们看到了Java中ThreadLocal是如何为线程安全开辟另一条大道的。虽然从JDK 1.0开始就已经有了通过将对象限制到线程之类而实现纯种安全的概念，并且很多开发者都有了自己的ThreadLocal实现，但是将ThreadLocal放入Java API让这一切变的更简单更标准。在设计你的并发应用时先想一想ThreadLocal变量。不要把ThreadLocal错误的理解为同步的替代品，这应该取决于程序是如何设计的。如果设计允许每个线程拥有对象的独立复本，那么ThreadLocal就可以出场了。