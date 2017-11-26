---
title: Java同步教程：什么是同步，如何实现以及为什么？
date: 2017-11-26 09:05:54
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2011/04/synchronization-in-java-synchronized.html

多线程和同步对于每个Java程序员来说都是一个十分重要的话题。拥有不错的多线程，同步，和线程安全方面的知识可以让你走在其他开发者的前面，但同时，这些概念也并不容易掌握。事实上想要写出正确的并发代码是一件非常困难的事情，就算是Java提供了一些内建的同步工具也一样。在这篇Java同步教程中我们将学习Java同步的意义，为什么Java需要同步，什么是Java同步关键字，使用**Java同步方法和代码块**的示例，当多线程代码缺失了同步结构的时候会发生什么，可以避免错误的小提示，while locking critical section in Java以及一些**Java同步的重点**。

因为Java提供了几种不同的结构来支持同步及锁，比如[volatile关键字](http://javarevisited.blogspot.sg/2011/06/volatile-keyword-java-example-tutorial.html)，atomic变量，通过java.util.concurrent.lock.Lock接口显式加锁以它们的实现，比如ReentrantLock和ReentrantReadWriteLock，这让理解同步与其它结构的区别甚至变得更为重要。

记住，想要写出正确的Java并发代码对于同步有一个清晰的认识是必不可少的，这可以避免出现诸如死锁，竞态条件，以及线程安全之类的多线程问题。我能肯定，从这篇**Java同步教程**学到的内容会对你有帮助。一旦你读完这篇教程，你可以继续阅读[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QpQC-tXlTeDKCAMXSPGMSy4AAAFf98S7nAEAAAFKAekuM3w/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=1.rU4rorQqilq1xpB9NCpA&slotNum=0&tag=javamysqlanta-20)来进一步完善你的观念。这本书是每个Java开发者都必读的。

# Java同步是什么
同步在Java中是一个很重要的概念，因为Java是一门多线程语言，多个线程以并行的方式运行来完成程序执行。在多线程环境中，Java对象的同步以及Java类的同步变得极度重要。通过使用Java关键字“**synchronized**”和“**volatile**”使得Java同步成为可能。

在Java中共享对象的并发访问引入了2类错误：线程冲突以及内存差异，而想要避免这些错误你需要合理的同步你的Java对象来允许2个线程互斥访问关键区块。

顺道一提，这篇教程是对我的博客[Java中HashMap是如何工作的](http://javarevisited.blogspot.com/2011/02/how-hashmap-works-in-java.html)以及[Java中HashMap与Hashtable的区别](http://javarevisited.blogspot.com/2010/10/difference-between-hashmap-and.html)的续写，如果你还没有读过它们，那么你有可能找到一些我在Java集合方面的经验。


# 为什么Java需要同步？
如果你的代码在多线程环境中执行，你需要为对象做同步，就是那些在多个线程间共享的对象，以避免任何状态的败坏以及各种各样意想不到的行为。Java的同步只有共享对象是可变的时才需要。如果你的共享对象是只读的或者是[不可变对象](http://javarevisited.blogspot.com/2013/03/how-to-create-immutable-class-object-java-example-tutorial.html)，那么你不需要同步，尽管是在多个线程里运行。同样如果多个线程对共享对象所做的操作是所有线程都只读取值，那么你也不需要同步。JVM保证**同步代码同一时间只被一个线程执行**。

概括地说，Java同步关键字提供下列并发编程的基本要素：

1) Java的同步关键字提供锁机制，保证对共享资源的互斥访问并防止数据冲突。

2) 同步关键字也可以阻止编译器重组代码，编译器对代码的重组可能会使在不使用synchronized或volatile关键字的时候引发一些微妙的错误。

3) 同步关键字涉及到加锁和释放锁。在进入**同步方法或代码块**之前，线程需要先取得锁，在这个时间点它从主存中读取数据然后缓存起来而当它释放锁时，它会将所有写入操作刷入主存以消除内存差异。

想了解更多内容，请再次阅读[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QpQC-tXlTeDKCAMXSPGMSy4AAAFf98S7nAEAAAFKAekuM3w/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=1.rU4rorQqilq1xpB9NCpA&slotNum=1&tag=javamysqlanta-20)，如果你还没有读过的话：

<div align=center>[![avatar](https://4.bp.blogspot.com/-lfCssbFikJM/Vuq8FT-fIfI/AAAAAAAAFIc/qzg-4N1k2-IYMNozFKliIQuWZph2YO9jQ/s320/Java%2BConcurrency%2Bin%2BPractice.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/QoKvaWoXAJfOGpz3DjWi0UUAAAFf9duSQgEAAAFKAWaWrUg/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=XICsuh7bbVGMzgyZybtX4w&slotNum=2&tag=javamysqlanta-20)</div>

# Java同步关键字
在Java 1.5之前同步关键字是Java唯一支持的共享对象同步机制。任何写在**同步块**或封闭在**同步方法**中的代码都是互斥的，同一时间只能被一个线程执行。你的Java代码可以拥有[静态同步方法和非静态同步方法](http://javarevisited.blogspot.sg/2012/03/mixing-static-and-non-static.html)以及同步代码块，但是你**无法**拥有**同步变量**。将同步关键字应用在变量上是非法的，将会引入编译错误。

你可以使用Java的volatile变量来代替同步变量，它会指示JVM线程从主存中读取volatile变量的值而且不对其进行缓存。Java的块级同步要优先于方法同步，因为通过使用块级同步，你只需要锁住关键代码块而不是整个方法。由于Java的同步附带着性能消耗，所以我们需要尽可能只同步那些绝对需要同步的代码。

# Java同步方法示例
将**同步关键字**应用到方法上很简单，只需要将synchronized关键字放在方法上就行了。我们需要注意的是静态同步方法锁住的对象是类对象，而非静态同步方法锁住的是当前对象（[this](http://javarevisited.blogspot.com/2012/01/this-keyword-java-example-tutorial.html)）。所以Java的静态和非静态同步方法并行运行是可能的。这是一个缺乏经验的的开发者在**编写Java同步代码**时会犯的错误。

{% codeblock lang:java %}
public class Counter{

  private static int count = 0;

  public static synchronized int getCount(){
    return count;
  }

  public synchoronized setCount(int count){
     this.count = count;
  }

}
{% endcodeblock %}

在这个Java示例中，同步机制不没有合理应用，因为getCount()和setCount()锁住的不是同一个对象，它们是可以并行运行的，而这就有可能产生错误的结果了。这里的getCount()会锁住Counter.class而setCount())会锁住当前对象（this）。为了让这段代码合理应用Java的同步机制，你要么将2个方法都改为静态或非静态的，要么使用Java的块级同步而不是方法同步。顺道一提，这也是上Java开发者在同步他们的代码时通常会犯的错误。

# Java块级同步示例
Java中使用**块级同步**与使用**同步方法**有些类似。唯一要注意的是如果用于锁住代码块的对象，下例中的Singleton.class是null，那么Java同步代码块会抛出一个[NullPointerException](http://javarevisited.blogspot.com/2012/06/common-cause-of-javalangnullpointerexce.html)。

{% codeblock lang:java %}
public class Singleton{

private static volatile Singleton _instance;

public static Singleton getInstance(){
   if(_instance == null){
            synchronized(Singleton.class){
              if(_instance == null)
              _instance = new Singleton();
            }
   }
   return _instance;
}
{% endcodeblock %}

这是一个很经典的[单例模式双重检查同步](http://javarevisited.blogspot.com/2014/05/double-checked-locking-on-singleton-in-java.html)示例。在这个**Java同步代码示例**中，我们只让关键的代码块（创建单例实例的代码块部分）保持同步以节省一些性能。

如果你让整个方法都同步了，那么每一个对这个方法的调用都会被阻塞，而实际上你只需要阻塞掉第一次创建单例实例的调用就行了。顺道一提，这并不是编写Java线程安全单例的唯一方法。你还可以使用Enum，或者懒加载来避免初始化期间的线程安全问题。

<div align=center>[![avatar](https://2.bp.blogspot.com/--yru71J7s5Q/Vuq7wClJZuI/AAAAAAAAFIQ/MTXEODqcE-ovWrdfqYpESWuBx2DpXEySw/s640/Double%2BChecked%2BLocking%2Bin%2BJava.jpg)](https://2.bp.blogspot.com/--yru71J7s5Q/Vuq7wClJZuI/AAAAAAAAFIQ/MTXEODqcE-ovWrdfqYpESWuBx2DpXEySw/s1600/Double%2BChecked%2BLocking%2Bin%2BJava.jpg)</div>

就算是使用了上面的代码，实际效果也有可能并不如你气相，因为在Java 1.5之前，双重检查同步会发生故障，甚至使用了volatile变量你也还能查看初始化到一半的对象。Java 5引入的Java内存模型和happens before保证解决了这个问题。想要了解更多关于[Java单例](http://javarevisited.blogspot.com/2011/03/10-interview-questions-on-singleton.html)的内容，看看它。

# Java同步关键字的要点
1. **Java同步关键字**是用来提供多线程对共享资源的互斥访问的。Java同步机制保证任何2个线程都不能同时或并发的执行一个同步方法。

2. 你只能将同步关键字用在同步方法或同步代码块上。

3. 一个线程无论何时在进入同步方法或同步代码块之前会先**取得对象锁**，而无论何时当它离开同步方法或同步代码块时它会释放对象锁。就算是因为任何错误或异常导致退出同步方法对象锁也会得到释放。

4. Java线程进入Java实例同步方法时取得的是**对象级锁**，而在进入Java静态同步方法时取得的是类级锁。

5. **Java同步关键字本质上是重入式的**，这表示当一个同步方法调用另一个同步方法而2者加锁的对象是同一个时，那么持有锁的[当前线程](http://javarevisited.blogspot.com/2011/02/how-to-implement-thread-in-java.html)无需再次取得被调用方法的锁就可以进入该方法了。

6. Java同步机制在Java同步代码块所使用的对象为null时会抛出NullPointerException，比如synchronized (myInstance)会在myInstance为null时抛出java.lang.NullPointerException。

7. **Java同步关键字的一个主要缺点**是它不允许并发读取，这潜在的限制了可伸缩性。你可以通过使用锁分离的概念并使用不同的锁来做读取和写入来克服Java同步关键字的这个限制。你会很高兴Java的java.util.concurrent.locks.ReentrantReadWriteLock已经提供了现成的ReadWriteLock实现。

8. **Java同步关键字的另一个限制**是它只能用于控制同一个JVM内共享资源的访问。如果你有多个JVM需要对一个共享文件系统或数据库做同步访问，那么Java同步关键字一点也不会满足你的需求。你需要为这种情况实现一种全局锁。

9. **Java同步关键字会招致性能消耗**。Java中的同步方法性能非常低，它会让性能降级。所以尽量在绝对需要同步的时候才在Java中应用它或者考虑只用Java的块级同步来锁住关键代码块。

10. **Java块级同步比Java的同步方法更好**，因为使用块级同步你可以只锁住关键代码块而避免因锁住整个方法而导致的性能降级。[Singleton类的获取Instance()方法](http://javarevisited.blogspot.com/2011/03/10-interview-questions-on-singleton.html)是围绕这个概念的一个好例子。看看这里。

11. 静态同步和非静态同步方法同时或并发运行的情况是有可能发生的，因为它们锁住的不是同一个对象。

12. Java内存模型从Java 5开始引入了一个变化，**任何使用volatile关键字定义的变量的读取和写入都是原子操作**（包括long和double），而直接访问原子变量要比使用同步代码访问变量更为高效。但是需要程序员更小心并集中注意力只避免内存差异。

13. 如果没用正确应用同步机制那么多个线程访问**Java同步代码时可能最终导致死锁或饿死**。想要了解[如何在Java中避免死锁](http://javarevisited.blogspot.com/2010/10/what-is-deadlock-in-java-how-to-fix-it.html)查看这里。

14. 根据Java语言规范**你不能在构造器上使用Java同步关键字**，这个非法的并且会导致编译错误。所以你不能同步Java的构造器，这看起来也是很合逻辑的，因为在创建某个对象的线程完成创建对象之前，其它线程对这个对象是不可见的。

15. **你不能将Java同步关键字应用到变量上**，也不能将volatile关键字用到方法上。

16. Java.util.concurrent.locks扩展了Java同步关键字的能力以支持编写更复杂的程序，因为它们提供了更多的功能，比如**重入**和**可中断锁**。

17. Java同步关键字同时同步了内存。事实上，Java同步关键字将整个线程内存与主存同步了。

18. 涉及Java同步的重要方法有wait()，notify()和notifyAll()，它们都定义在Object类中。你知道它们为什么定义在java.lang.Object中而不是java.lang.Thread中吗？你应该能找到[一些合理的原因](http://javarevisited.blogspot.com/2012/02/why-wait-notify-and-notifyall-is.html)。

19. **不要将non-final属性用在Java的块级同步上**。因为non-final属性的引用任何时候都可能发生改变，然后不同的线程的同步对象可能就不一样了，也就是说完全没有同步。一个使用non-final属性同步的例子：

{% codeblock lang:java %}
private String lock = new String("lock");
synchronized(lock){
    System.out.println("locking on :"  + lock);
}
{% endcodeblock %}

如果你在Netbeans和Intelij之类的IDE中编写类似上面这样的同步代码，你可能会得到“**Synchronization on the non-final field**”的警告。

20. **不推荐使用String对象来锁住Java同步代码块**，因为[String是不可变对象](http://javarevisited.blogspot.com/2010/10/why-string-is-immutable-in-java.html)，不可变字符串和限定字符串会被保存到String池中。所以一旦出现代码中其它地方或者第三方类库使用同一个String的情况，那么它们就会锁住同一个对象，尽管它们完全不相关，而这就可能会导致意料不到的情况以及糟糕的性能。建议使用new Object()来做**Java块级同步的对象锁**，而不是String对象。

{% codeblock lang:java %}
private static final String LOCK = "lock";   //not recommended
private static final Object OBJ_LOCK = new Object(); //better

public void process() {
   synchronized(LOCK) {
      ........
   }
}
{% endcodeblock %}

21. Java类库中的Calendar和[SimpleDateFormat类都不是线程安全的](http://javarevisited.blogspot.sg/2012/03/simpledateformat-in-java-is-not-thread.html)，在多线程环境中使用时需要**额外同步处理**。

最后,

<div align=center>[![avatar](https://1.bp.blogspot.com/-qi0tDL9E1q4/Vuq71slxIDI/AAAAAAAAFIg/Y5GYkJhnp54HbiqLLnk08a6tN_lC4h7tA/s400/Dont%2Bsynchronize%2Bwith%2Bautoboxed%2Bvalue.jpg)](https://1.bp.blogspot.com/-qi0tDL9E1q4/Vuq71slxIDI/AAAAAAAAFIg/Y5GYkJhnp54HbiqLLnk08a6tN_lC4h7tA/s1600/Dont%2Bsynchronize%2Bwith%2Bautoboxed%2Bvalue.jpg)</div>

关于Java同步机制可能最重要的一点是在缺乏同步关键字或者其它结构的时候，比如volatile变量或atomic变量，编译器、JVM及硬件都可自由对代码及数据做优化，假定，重组或缓存，这会在代码中引发微妙的并发bug。通过使用volatile，atomic变量或synchronized关键字等同步机制，我们可以指示编译器和JVM不要做这些事情。

**更新1**：最近我在网上读过好几篇**Java同步与并发**方面的文章，我无意中发现了Jeremy Manson的博客，他在google上班，曾经效力于JSR 133 **Java内存模型**，对于它的一些博文我想推荐给每个Java开发者，他已经用简单通俗易懂的语言介绍了关于并发编程、同步机制和挥发性的一些细节，这里是[原子性、可见性和重组](http://jeremymanson.blogspot.com/2007/08/atomicity-visibility-and-ordering.html)的链接。

**更新2**：我非常感谢我的读者，他们在这篇文章里留下了一些深刻的评论。他们分享了很多不错的知识及经验，为了更好的暴露这些内容，我将他们的评论包含在文章主体中，让新读者能够从中受益。

**@Vikas 评论**
一篇非常不错的Java同步关键字的综合文章。老实的说我从来没有在一个地方读过所有这些关于块级同步或方法同步的细节。你可能需要突出一些同步关键字在Java中的限制，这些是使用新并发包和Lock接口的显式加锁所致力于解决的。

1. 同步关键字不允许为读取和写入分离锁。as we know that multiple threads can read without affecting [thread-safety of class](http://javarevisited.blogspot.sg/2012/01/how-to-write-thread-safe-code-in-java.html), synchronized keyword suffer performance due to contention in case of multiple readers and one or few writer.

2. 如果一个线程正在等待锁，那么就没有办法设置超时，这个线程可以无限期的等待锁。
 
3. 同样如果一个线程正在等待锁被获得，那么也没有办法中继这个线程。
 
所有同步关键字的这些限制都是Java 5中ReadWriteLock和ReentrantLock所致力于解决的。

**@George 评论**
关于你的Java同步要素与最佳实践，我的几点看法：
 
1) 同步关键字在内部实现上使用了双位元组码指令MonitorEnter和MonitorExit，这由编译器生成。编译器还保证在不同的代码路径中对于每一个MonitorEnter都有一个MonitorExit与之匹配，比如正常执行和因为异常导致的异常执行。

2) java.util.concurrent打包了锁机制与同步关键字提供的不同，它们通常使用ReentrantLock，这个类内部使用了CAS操作，volatile变量和atomic变量来获得更好的性能。
 
3) 使用同步关键字，你需要翻译锁，一旦你退出一个同步方法或代码块，没有任何办法能让你将锁传递给另一个方法。java.util.concurrent.locks.ReentrantLock提供了控制获取和释放锁的方法来解决这个问题，这表示你可以在A方法中获取锁然后在B方法中释放锁，如果它们所需要锁住的对象是同一个。不过这是有风险的，因为编译器既不会检查也不会警告你任何锁的意外泄漏。这可能会将等待同一对象锁的其它线程阻塞掉。

4) 优先选择ReentrantLock而不是同步关键字，它在获取、释放锁上提供更多控制，并且对比同步关键字有更好的性能。
 
5) 每个尝试获取锁的同步方法在锁可用之前都会无限期阻塞。反之，如果锁不可用使用java.util.concurrent.locks.ReentrantLock的tryLock()方法则不会阻塞。
话虽如此, 我必须说, 有很多有用的信息。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)