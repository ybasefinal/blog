---
title: Java Concurrency in Practice这本书还适用于Java 8吗？
date: 2017-11-19 09:22:06
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2016/12/is-java-concurrency-in-practice-still-relevant-in-era-of-java8.html

在我的一篇关于[12个中级程序员必须阅读的高级Java书籍-第一部分](http://www.java67.com/2016/06/12-must-read-advance-java-books-for-intermediate-programmers.html)博文中，一位叫Shobhit的读者问到了这个问题。我真的很喜欢这个问题，并且认为很多Java程序员可能会在别人推荐他阅读**Java concurrency in Practice**时有同样的疑虑。当这本书在2006年刚刚出版时，Java世界对于Java 1.5带来的并发改变仍然不确定，我认为这是首次尝试改善Java的内置的多线程和并发。很多Java程序员甚至没有意思到推出了新的工具API，如[CountDownLatch](http://www.java67.com/2015/06/java-countdownlatch-example.html)，[CyclicBarrier](http://www.java67.com/2015/06/how-to-use-cyclicbarrier-in-java.html)，ConcurrentHashMap以及更多。这本书给他们带来了针对这些工具的无缝介绍，以及他们应该如何在Java中使用这些工具来编写高性能并发程序。

这就是这本书的概貌，当你向很多Java开发者询问他们是怎么发现["Java Concurrency in Practice"](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=0&tag=javamysqlanta-20)的时候他们都会这样回答你，但是我的观点和他们有些微的不同，这也是为什么我仍在为想要掌握并发概念的Java新手或中级程序员推荐Java Concurrency in Practice。

这本书最重要的地方在于清楚的介绍了并发编程的概念及基本原理，比如可见性，定序，[线程安全](http://javarevisited.blogspot.com/2012/01/how-to-write-thread-safe-code-in-java.html)，[不可变性](http://javarevisited.blogspot.com/2013/03/how-to-create-immutable-class-object-java-example-tutorial.html)，并行性等等。它还跟着解释了为什么大多数Java并发程序都写错了，以及Java程序员经常会犯的错误，这些错误通常会引发竞态条件，死锁，活锁，内存干扰和简单的计算错误等多线程问题。

书中在介绍正确的做法之前使用表情符号来指出不好的做法，这种方式带来了巨大的帮助，不仅是纠正了一些Java程序员之前的误解，还在Java程序员之间撒下了关于多线程及并发正确知识的种子。

无疑多线程和并发是很难的，他们都难用在代码中正确应用，也很难理解和解读。我已经见过很多程序没有能力想像出当不同的数据应用在同样的代码上时多线程之间如何交互。很象经典的递归概念，也是对于大多数程序员容易，对于其他人难得理解并且应用到现实场景中。

[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=1&tag=javamysqlanta-20)为Java世界做出的最大贡献并不是让并发变得简单，而是提供正确的清晰的知识，而这正是Java所缺乏的。因为我做过很多次面试，所以我清楚程序员经常对线程以及如何使用它们有误解。

甚至很多4、5年经验的Java程序员不理解[volatile变量的工作原理](http://javarevisited.blogspot.sg/2011/06/volatile-keyword-java-example-tutorial.html)，他们只知道当你使用volatile变量时，会在每次做比值操作时从主存中取值，这虽然是正确的但不是完全的答案。

没人和他们讲过Java内存模型以及volatile变量如何影响[JIT](http://www.java67.com/2013/02/difference-between-jit-and-jvm-in-java.html)和[JVM](http://www.java67.com/2016/08/10-jvm-options-for-java-production-application.html)背后的代码和计算指令定序等优化，进而可能引发微妙的逻辑bug。他们没有意识到volatile是如何保证一个线程所做的会被另一个线程可见等等。他们不知道内存栅栏及它对可见性的影响。

Java concurrency in Practice教会了很多Java程序员这个概念。事实上，我也会承认在阅读这本书之前，我也对很多基本多线程和并发概念有误解，比如定序，可见性以及final变量和安全发布的微妙影响。

<div align=center>[![avatar](https://3.bp.blogspot.com/-7fVDkozYaAg/WFASUZHVuwI/AAAAAAAAHg8/27FDFY9uLoof_ghFVynaB5kgpaRq43dVACEw/s320/Java%2BConcurrency%2Bin%2BPractice%2Bstill%2Brelevant%2Bin%2BJava%2B8%2Bera.jpeg)](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=2&tag=javamysqlanta-20)</div>



现在，回到Java 8，是的从Java 1.5到Java 8，JDK已经拥有了大量可以实现并发和设计更好的并发程序的工具。从JDK 7引入的[fork-join pool](http://javarevisited.blogspot.sg/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html)，到java 8的CompleteableFutures以及更为重要Java 8带来的新[lambda表达式](http://javarevisited.blogspot.com/2014/02/10-example-of-lambda-expressions-in-java8.html)的函数式编码风格。

你同时还获得了并行流，不需要你编码就可以利用并发带来的好处。将并发从程序开发人员转移到API开发人员的总体思路也让它变的稍微简单了一些，并且减少了实现Java 并发的风险。这也表示你可以调用一些个Java方法和多线程来执行批量任务，而且不需要写一行关于线程，同步关键字或wait-notify方法的代码。

无疑，Java程序员需要学习这些新工具来确保自己不落伍，而一本[Java 8 in Action](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/Java-Action-Lambdas-functional-style-programming/dp/1617291994/ref=as_at?creativeASIN=1617291994&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=3&tag=javamysqlanta-20)在这方面确实会有帮助。它为你介绍了Java 8的所有新改变，以及不仅仅教你如何在日常任务中使用它们，还解释了它们背后的动机，帮助你上更好的理解大局。

<div align=center>[![avatar](https://1.bp.blogspot.com/-3WD20t_mzPg/WFASSp0gaoI/AAAAAAAAHg4/2asXFt0HE9MJjW_Uz4wwe0B_zT5Cxzq1QCEw/s320/Java%2B8%2Bin%2BAction%2Bbest%2Bbook%2Bto%2Blearn.jpg)](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/Java-Action-Lambdas-functional-style-programming/dp/1617291994/ref=as_at?creativeASIN=1617291994&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=4&tag=javamysqlanta-20)</div>


尽管Java Concurrency in Practice就其目前情况没有完全覆盖这些概念及工具，但它仍然是一本难以估量的学习[线程](http://javarevisited.blogspot.sg/2011/02/how-to-implement-thread-in-java.html)，[并发]基础(http://javarevisited.blogspot.com/2015/05/top-10-java-multithreading-and.html)以及Java程序语言提供的[多线程](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html)工具的书。

它仍然是任何想要学习和掌握多线程和并发的Java程序员必读的一本书，这也是Java程序开发最大的优势。我感觉Brian Goetz，Joshua Bloch和所有其他作者，给了Java开发者这样一本权威的书来理解让人困惑的又必须了解的多线程和并发关键概念。

话说回来，就如世界上很多Java开发者一样，我也很想看到一本更新，更应时的Java Concurrency in Practice来覆盖Java 6，7，8所引入的工具和方法，就好像[Head First design pattern](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/dp/0596007124/ref=as_at?creativeASIN=0596007124&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=5&tag=javamysqlanta-20)的更新版本一样，覆盖了Java 8并且教你哪些模式使用Java 8的新特教更容易实现。

所以，让我们期待Brian Goetz，Joshua Bloch和这本书的其他作者能够听到这个请求，然后在2017年为Java开发者带来一本全新的[Java Concurrency in Practice](http://aax-us-east.amazon-adsystem.com/x/c/QjhCRKoai_k2Oq5vVV25Y40AAAFf0d3U6QEAAAFKAYcobSg/http://www.amazon.com/dp/0321349601/ref=as_at?creativeASIN=0321349601&linkCode=w61&imprToken=NtcuIQc8xrO5vNE2nw36-A&slotNum=6&tag=javamysqlanta-20)。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)