---
title: 5本适合学习Java多线程编程的书
date: 2017-11-22 09:01:29
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/06/5-books-to-learn-concurrent-programming-multithreading-java.html

书对于学习新知识非常重要，尽管在电子商务时代书在网络和博客面前已经推动不少亮点，我依然会阅读并推荐一些书，让我任何话题上都尽量拥有完整权威的知识，比如并发编程。这篇文章中，我将分享**5本最适合学习Java并发编程的书**。这些书涵盖了基础，从如何创建和启动线程，并行编程，[并发设计模式](http://java67.blogspot.com/2015/12/producer-consumer-solution-using-blocking-queue-java.html)，并发带来的好处以及因为多线程而引发的陷阱，争议，难题等等。学习并发编程是一个困难的任务，不仅仅是在Java中，在其它如C++或一些现代的类似于JVM的语言如Groovy，Scala，Closure，和JRuby。

因为我已经从事Java开发超过10年了，所以我可以肯定的讲它是最适合开始学习并发编程的语言，因为它们结构，[synchronized](http://javarevisited.blogspot.com/2011/04/synchronization-in-java-synchronized.html)、[volatile](http://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html)、和[final](http://java67.blogspot.com/2015/07/how-to-use-final-keyword-in-java-example.html)等关键字的内置并发支持，最后也由于在网络和社区存在大量有帮助的信息。

不管如何，编写代码和从错误中学习知识是不可替代的，不过你需要一本书来指导、训练和教导你，那些其它人已经学到的线程、并发和并行编程。

## 适合Java程序员的5本顶级多线程和并发书

这里是我列出的用来掌握Java线程基础、多线程和原理以及并发编程模式的书籍清单。它们会教会你该做什么也会教你不该做什么。因为多线程是一个棘手的话题，所以最好参考多本书籍，因为你会发现你可能更容易与其中某些书的作者产生共鸣。


### [The Art of Multiprocessor Programming](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/https://www.amazon.com/Art-Multiprocessor-Programming-Revised-Reprint/dp/0123973376/ref=as_at?creativeASIN=0123973376&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=0&tag=javamysqlanta-20)
理想地说，这本书应该是每个程序员第一本要读的关于并发和多核处理器编程的书，它覆盖了一些如[生产者-消费者](http://javarevisited.blogspot.com/2015/06/java-lock-and-condition-example-producer-consumer.html)和[读取者-写入者](http://javarevisited.blogspot.com/2014/07/top-50-java-multithreading-interview-questions-answers.html)问题之类的学术材料，并且解释了一些多核处理器的和共享内存的基础。

Maurice Herlihy和Nir Shavit修订的The Art of Multiprocessor Programming也是那些仅仅只知道如何使用Java线程但是不熟悉并发编程基本原理的Java开发者的必读物，比如[关键区块](http://java67.blogspot.com/2016/04/why-double-checked-locking-was-broken-before-java5.html)，[互斥](http://java67.blogspot.com/2013/01/difference-between-synchronized-block-vs-method-java-example.html)，[锁机制](http://javarevisited.blogspot.com/2014/10/how-to-use-locks-in-multi-threaded-java-program-example.html)，[共享内存](http://javarevisited.blogspot.com/2012/01/memorymapped-file-and-io-in-java.html)，[内存栅栏](http://javarevisited.blogspot.com/2015/10/133-java-interview-questions-answers-from-last-5-years.html)以及同步机制等等。我必须说，你得读了这本书才能了解它的真正价值，它太棒了。

简而言之，一本不仅仅是Java程序员，而且也是任何想要学习并发编程基础如C++，C#，Scala的程序员都必须阅读的真的很了不起的书。

<div align=center>[![avatar](https://3.bp.blogspot.com/-N1UVzZTnGWk/V1zkfsFM-TI/AAAAAAAAGM4/2xJRGeNia0YGmvMRqRLBS5lL09zT9RNsQCKgB/s320/The%2BArt%2Bof%2BMultiprocessor%2BProgramming.png)](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/https://www.amazon.com/Art-Multiprocessor-Programming-Revised-Reprint/dp/0123973376/ref=as_at?creativeASIN=0123973376&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=1&tag=javamysqlanta-20)</div>

### [Java Thread 3rd edition](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/http://www.amazon.com/dp/0596007825/ref=as_at?creativeASIN=0596007825&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=2&tag=javamysqlanta-20)
我读过的第一本书，尤其是在Java多线程中，是由Scott Oaks和Henry Wong编写的Java Thread第二版。这本书很了不起，它教授了创建线程和如何使用它们的基础，并提供了一些简单但不琐碎的例子。它有一个章节写的最棒，教授[如何使用在Java中使用wait和notify机制](http://javarevisited.blogspot.com/2015/07/how-to-use-wait-notify-and-notifyall-in.html)。唯一的拖后腿的是第2版出版的时候没有覆盖新的并发类库，如Java 1.5引入的[CountDownLatch](http://java67.blogspot.com/2015/06/java-countdownlatch-example.html)，[CyclicBarrier](http://java67.blogspot.com/2015/06/how-to-use-cyclicbarrier-in-java.html)，或[Semaphore](http://javarevisited.blogspot.com/2012/05/counting-semaphore-example-in-java-5.html)。

新版**Java Thread 3rd edition**填补了那个空缺，现在它完全覆盖了J2SE 5.0那些新的并发和同步工具类库。如果你是一个初学者，不清楚什么是线程，它们有什么帮助以及如何使用它们来创建多线程程序，那么这本书是你应该读的。

尽管这个本的焦点是Java编程语言，它也对学习多线程基础有帮助，如果你想要从其它语言上开始的话，比如Scala。

<div align=center>[![avatar](https://1.bp.blogspot.com/-Y33W13Sb5d8/V1ziMmjjUvI/AAAAAAAAGMo/R7j8eiUukesG4GeYDC5xknsAP5VsKUn6wCKgB/s320/Java%2BThreads%2B2nd%2BEdition.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/http://www.amazon.com/dp/0596007825/ref=as_at?creativeASIN=0596007825&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=3&tag=javamysqlanta-20)</div>

### [Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=4&tag=javamysqlanta-20)
很多Java Concurrency in Practice的铁杆粉丝会因为这本书没有被排在清单的最上面而发怒。然而，事实上我在从初学者成为高级程序员的过程中就已经买过了这本书，肯定的，这本书涵盖了Java并发的高级面。另外，如果你问我只给Java程序员推荐一个关于并发和多线程的书，那么肯定是这本了。

Java Concurrency in Practice是Java开发者在并发和并发编程上的圣经，对于每个对并发编程认真的开发者来说都是必读的。

其中一个亮点是它们的用辞，可谓晶莹剔透，这对于解释一个复杂又容易产生困惑的话题是至观重要的。另一个亮点是它们的作者团队，囊括了Java并发专家Brian Goetz，编写过[Effective Java](http://java67.blogspot.com/2016/06/12-must-read-advance-java-books-for-intermediate-programmers.html)和[Java Collection API](http://javarevisited.blogspot.com/2013/02/concurrent-collections-from-jdk-56-java-example-tutorial.html)的Joshua Bloch，Doug Lea，这位是java.util.concurrent包中很多类的作者，比如ConcurrentHashMap，Java中最常被用到的并发集合。

一句话，每个Java开发者都必须在自己的书架和手机里放一本Java Concurrency in Practice，一旦有时间就可以随时阅读。

<div align=center>[![avatar](https://1.bp.blogspot.com/-nF1aJbFeS5I/V1ziOGTOAcI/AAAAAAAAGMo/pQ3N5qinjlAdvB2R7-tRDdCEpxauDkksACKgB/s320/Java%2BConcurrency%2Bin%2BPractice.jpg)](http://java67.blogspot.com/2015/05/best-book-to-learn-java-for-beginners.html)</div>

### [Concurrent Programming in Java: Design Principles and Patterns](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/https://www.amazon.com/Concurrent-Programming-Java%C2%99-Principles-Pattern/dp/0201310090/ref=as_at?creativeASIN=0201310090&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=5&tag=javamysqlanta-20)
这本书是Doug Lea的另一本关于并发和多线程的珍宝。这本书也是为有经验以及高级Java程序员准备的，这些人知晓Java多线程的基础知识，也了解一些并发知识。

这本书解释了很多关于设计、模式、和Java编程语言内建的同步特性。书中很多模式和设计都被Doug Lea用到了java.util.concurrent包中。这本书最好的用处是用于参照上面的例子，学习Doug Lea本人所写的代码。

这本书还介绍了[Java内存模型](http://javarevisited.blogspot.com/2014/05/double-checked-locking-on-singleton-in-java.html)，这对每个Java程序员都非常重要。不过我还是建议任何初学者都先阅读Java concurrency in Practice的Java内存模型章节，因为它们语言稍微更容易懂一些。

<div align=center>[![avatar](https://4.bp.blogspot.com/--kO3f3ESgQU/V1zkcA6lgfI/AAAAAAAAGMs/fJT6tG6060Q9h6Kn_yoD3o5-uVMNWiCYQCKgB/s320/Concurrent%2BPRogramming%2Bin%2BJava%2BDoug%2BLea.jpg)](http://java67.blogspot.com/2015/07/5-books-learn-java-8-functional-programming.html)</div>

### [Programming Concurrency on the JVM](http://aax-us-east.amazon-adsystem.com/x/c/QpMPr2URpmPsKZCMr1sA6fcAAAFf4T5IDgEAAAFKAchP6jo/https://www.amazon.com/Programming-Concurrency-JVM-Mastering-Synchronization/dp/193435676X/ref=as_at?creativeASIN=193435676X&linkCode=w61&imprToken=iykcrHKs4ZK4mO.nImy8Tw&slotNum=6&tag=javamysqlanta-20)
这是一本在并发编程、同步机制和多线程等领域相对较新的书。它由一位我最喜欢的作者编写，Venkat Subramaniam，它不仅解释了Java中的概念，同时也解释了其它JVM编程语言如[Scala](http://javarevisited.blogspot.com/2014/01/top-5-free-books-to-learn-scala-programming-PDF.html)，Groovy，Closure，和JRuby。这本书的最大亮点是**Software Transnational Model（STM）**和**Actor-based并发**。这本书解释了并发编程的新技术，可以让你进行比较然后选择最前沿的东西。我觉得它的最大卖点就是将大量新技术写入书中。

<div align=center>[![avatar](https://4.bp.blogspot.com/-j_8Y0J-TARs/V1zkeJz-mqI/AAAAAAAAGM0/8bZ7njnf4n8q7Pk0gh5hstUBTHhXPPquQCKgB/s320/Progrmaming%2BConcurrency%2Bon%2Bthe%2BJVM.jpg)](http://javarevisited.blogspot.com/2014/07/top-5-java-performance-tuning-books.html)</div>

这些就是我的**5本顶级Java并发和多线程书籍**，它们会对学习并发编程和利用今天的先进多核处理器有帮助。所有书都同时对初学者和有经验的Java程序员有用，可以填补基础与学习可靠的和经受过考验的设计原则、模式、最佳实践以及需要避免的东西2才之间的间隙。话虽如此，但是如果你发现了任何其它并发和线程方面的好书，请告知我们。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)

P.S. - 如果你想要找的是成为专家级Java开发者的Java书籍目录，你必须看看[10本为经验丰富的程序员准备的Java书籍](http://javarevisited.blogspot.com/2013/01/top-5-java-programming-books-best-good.html)。

P.S. - 如果你想提高你的编码技能，不管任何编程技巧，那么你应该看看这[5个永恒的经典编码书籍](http://java67.blogspot.com/2016/02/5-books-to-improve-coding-skills-of.html)。
