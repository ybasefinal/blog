---
title: 为什么要在Java中使用线程
date: 2017-11-13 16:20:09
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2017/03/why-we-use-threads-in-java.html

一句话，我们使用线程同时处理多件事情让Java程序变的更快。从技术角度讲，线程能让你实现了Java程序的并行。由于CPU运转非常快并且现在的电脑都是多核的，单单只是一个线程不可能完全利用多核带来的好处，也就是说你花大价钱够买的硬件大部分时间都处理空闲状态。通过使用多线程，你可以最大化利用多核的优点来同时服务于多台终端并且服务的更快。因为，在现今快节奏生活中，响应时间决定一切，这也是为什么你会需要多核CPU，但如果你的程序不充分利用所有资源，那这些东西的加入就显的没有意义了，多线程是Java程序开拓CPU巨大计算能力的一种方式。

在Java中使用线程还有一个目的，同时处理多个任务。比如，在用户界面程序中，你想要同时渲染所有窗口，你也想要捕获用户动作如按键和命令，你还有可能想大网络上下载或者上传什么东西。

如果你把这些任务全部放在一个线程中，那它们会循序渐进的执行，比如首先渲染窗口然后捕获命令最终上传你的最高分到网络上。这可能会让你的游戏或程序出现问题，因为界面会在你执行其他任务的时候止住。而通过在Java中使用多线程你可以独立的执行这些任务。

## Java中使用多线程的原因

每个Java程序至少拥有一个线程，也就是执行你的main方法的主线程。还有很多被JVM使用的线程比如[守护线程](http://www.java67.com/2016/06/difference-between-daemon-vs-user-thread-in-java.html)，它们的作用是垃圾收集及其他管家任务。作为一名程序开发者，你也可以添加新的用户线程让程序更快更高效。这里对Java中使用多线程的原因及场景做下说明：

**1) 让一个任务与另一个任务并行比如渲染和事件处理**
GUI程序比如Swing和Java FX GUIs是Java中使用多线程最好的例子。在一个传统GUI程序中，用户触发一个动作比如从网络上下载一个文件或者从硬盘加载游戏模块。这些动作需要一些时间来完成，但是你又不能把界面卡住，因为用户会认为你的程序挂了。相反，你需要分出一个线程来执行这些消耗时间的任务，并持续显示一些无关的消息给用户，或者让他在同一时间去做其它事件以保证你的GUI能活下去。这只能通过Java的多线程来实现。

**2) 充分利用CPU的能力**
另一个在Java中使用多线程的原因是通过充分利用CPU的能力来提升吞吐量。比如，如果你有一台32核的机器而你只使用其它的一核来支撑1000个终端，并且假设你的程序有CPU资源限制，那么你可以通过使用32个线程让吞吐量提升32倍，这将完全利用你的32核CPU。你可以更深层次的读一读关于Java性能优化的书籍比如Scott Oaks的[Java Performance The Definitive Guide](http://www.amazon.com/Java-Performance-The-Definitive-Guide/dp/1449358454?tag=javamysqlanta-20)了解更多多线程所带来的性能影响。

<div align=center>[![avatar](https://3.bp.blogspot.com/-lJ4oXHXAePk/WL4EECLFupI/AAAAAAAAIEM/vZ9mSRuOJiYYvjTvMvt3EUGn_JJ9O-32QCLcB/s320/Java%2BPerformance%2BDefinitive%2BGuide.jpg)](http://www.amazon.com/Java-Performance-The-Definitive-Guide/dp/1449358454?tag=javamysqlanta-20)</div>

**3) 消减响应时间**
通过将大的问题分割成小块问题并在多个线程中处理它们来加速计算，你还可以减少响应时间。比如，map-reduce模式就是将大问题分割成小问题并单独处理它们，Java还支持用[Fork-Join 线程池](http://javarevisited.blogspot.sg/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html)来做这件事情。

**4) 同时服务钓鱼台设备**
使用多线程显著提升程序性能的一个最常用的场景是客户端-服务器应用。一个单线程应用表示同一时间只能有一个客户端面连接到服务器，而一个多线程服务器则表示同时可以有多个客户端连接到服务器上。这表示下个客户端不需要一直等到你的程序处理完上个客户端的请求。

就像下面的例子一样，你可以看见多个请求同时被我们的多线程服务器处理。

<div align=center>[![avatar](https://1.bp.blogspot.com/-GaNmFgdOUSE/WLI6YDJuZrI/AAAAAAAAICI/_5oK5lrH-iAuxZjZf_WEADfuYakMt6s-QCLcB/s400/threading_java.png)](https://1.bp.blogspot.com/-GaNmFgdOUSE/WLI6YDJuZrI/AAAAAAAAICI/_5oK5lrH-iAuxZjZf_WEADfuYakMt6s-QCLcB/s1600/threading_java.png)</div>

另外，多线程技术并不是随便就可以使用的，使用它会带来很大的挑战。你只能将应用吞量提升到某一个水平，一旦线程的数量提升到了某一个阈值，它们会开始互相竞争CPU资源从而引发上下文切换。上下文切换的意思是正在使用CPU的线程暂停，另一个线程被分配执行时间。当发生这种情况时，线程通常会丢失掉它所有的缓存数据。如果另一个核心唤醒了它，那它需要从零开始重建缓存。

多线程技术同时引入了一系列被称为多线程问题的难题，比如死锁，活锁，内存差异错误，竞态条件及饿死。另外测试涉及多线程的Java程序来也相当困难。你无法预测使用多线程又不使用同步时的执行顺序。你还应该读一读[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，了解如何在Java中高效的使用线程。

<div align=center>[![avatar](https://2.bp.blogspot.com/-1dQwdLVG7XE/WL4ELp_-ImI/AAAAAAAAIEQ/TXctVRUUANIsHp6qBjnizV4Xrd2iosFlQCLcB/s320/Java%2BConcurrency%2Bin%2BPractice%2Bstill%2Brelevant%2Bin%2BJava%2B8%2Bera.jpeg)](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)</div>

另外，并行会受限于关键区块的大小，也就是必须在同一个线程中同时执行的代码块。如果你的关键区块非常长，那最终所有线程都会处于等待中，然后你的程序就会看起来像一个单线程程序。

以上就是所有关于**为什么要在Java中使用多线程**。使用线程的基本原理和使用多个工人来完成一个任务类似，但是你需要记住并不是所有任务都能通过分配多个工人来提前完成的，比如9个妈妈也不可能在1个月内生出一个小孩。同样的，仅仅只是创建多个线程来加速你的程序也是不可行的。你只能通过将受限于CPU资源的任务分割给多线程来提升性能，比如大型计算。如果你的程序受限于IO，那你可能需要考虑其它技术比如更快的磁盘来提升性能。

你也应该考虑与多线程相关的问题，因为在多线程之间处理同步以及阻止类似死锁、活锁、饿死和内存差异问题并不容易。通常你只需要一个单线程因为它让你的编码变的容易，你也不需要考虑同步任何东西如果你的程序是单线程的。

我建议你找一本优秀的多线程及设计的书读一读，比如[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，了解更多Java中使用多线程带来的风险及报酬。

其它你可能喜欢的**有关多线程的文章**
* Java中Thread类的start()和run()方法的不同之处? ([回答](http://javarevisited.blogspot.sg/2012/03/difference-between-start-and-run-method.html))
* Java中如何实现线程间通讯? ([回答](http://javarevisited.blogspot.sg/2013/12/inter-thread-communication-in-java-wait-notify-example.html))
* Java中如何连接多个线程? ([回答](http://javarevisited.blogspot.sg/2013/02/how-to-join-multiple-threads-in-java-example-tutorial.html))
* Java中如何编写线程安全代码? ([回答](http://javarevisited.blogspot.sg/2012/01/how-to-write-thread-safe-code-in-java.html))
* Java中如何避免死锁? ([回答](http://javarevisited.blogspot.sg/2010/10/what-is-deadlock-in-java-how-to-fix-it.html))
* Java中进程和线程的真正不同之处? ([回答](http://javarevisited.blogspot.sg/2015/12/difference-between-thread-and-process.html))
* Java中如何使用thread-local变量? ([回答](http://javarevisited.blogspot.sg/2012/05/how-to-use-threadlocal-in-java-benefits.html))