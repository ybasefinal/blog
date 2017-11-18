---
title: Java的数组可以是volatile的吗
date: 2017-11-17 15:19:39
tags: 多线程
---
翻译自：http://javarevisited.blogspot.ca/2017/01/can-we-make-array-volatile-in-java.html

这是问题我已经在我写的[50个多线程面试题](http://javarevisited.blogspot.sg/2014/07/top-50-java-multithreading-interview-questions-answers.html)中分享过。是的，你可以将Java的任何数组变成volatile的，完成不会有问题，编译器不会报错，JVM也不会抛异常，但是棘手的部分是你为什么要把数组变成volatile以及**变成volatile后会对数组产生什么影响**？为了回答这个问题，你需要同时熟悉[volatile修饰符](http://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html)和[Java内存模型](http://javarevisited.blogspot.sg/2011/04/synchronization-in-java-synchronized.html)，否则，回答这个问题会变得很困难，这也是为什么它会是一个棘手的Java面试题的原因。在详细回答这个问题之前，我们会回顾一下Java的volatile关键字是什么东西以及它为多线程和并发带来了什么保证。

## Java的volatile关键字是什么东西？
volatile是Java的一种修饰符，它只能应用在成员变量上，可以是[实例变量和类变量](http://javarevisited.blogspot.com/2012/02/difference-between-instance-class-and.html)，并且支持[原型和引用类型](http://javarevisited.blogspot.sg/2015/09/difference-between-primitive-and-reference-variable-java.html)。它提供了一个happens-before保证，保证对一个volatile变量的写操作会发生在读操作之前。这能保证任何对volatile对象或原型的修改都会对所有线程可见，也就是说它提供了可见性保证。

[volatile修饰符](http://www.java67.com/2012/08/what-is-volatile-variable-in-java-when.html)还提供指令定序保证，因为编译器不能对和volatile变量（原型和对象）有关的任何代码或操作做指令重排，但是更重要的需要的记得应该是volatile变量不支持**原子性**（除了对double类型的volatile变量做写入操作）和**互斥**，这也是volatile和[同步](http://www.java67.com/2013/01/difference-between-synchronized-block-vs-method-java-example.html)关键字的主要区别。

对于使用volatile关键字有一些限制，比如，你不能同时将成员变量声明为[final](http://javarevisited.blogspot.sg/2016/09/21-java-final-modifier-keyword-interview-questions-answers.html)和volatile，但你可以将Java的[静态变量](http://www.java67.com/2016/05/difference-between-static-and-nonstatic-member-variable-in-java.html)声明为volatile。

如果你想了解更多有关Java volatile变量的知识，我推荐你阅读[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，它会为你提供更彻底的和完整的Java volatile修饰符的介绍和用途。

## Java的数组可以声明为volatile吗？
现在，回到正题，我们可以将Java数组声明为volatile吗？答案是，可以，你可以将Java的任何数组（既可以是原型也可以是引用类型，比如[int数组](http://www.java67.com/2015/07/array-concepts-interview-questions-answers-java.html)和[String数组](http://www.java67.com/2012/09/java-program-to-convert-string-arraylist-to-string-array.html)）声明为volatile，但是只有对数组引用的修改才会被所有线程可见，而不是整个数组。这段话的意思是，假设你有一个叫做primes的引用变量，如下：

{% codeblock lang:java %}
protected volatile int[] primes = new int[10];
{% endcodeblock %}

然后如果你将一个新数组赋值给primes变量，修改会被所有线程可见，但是对单个数组单元的修改不会被volatile的可见性保证覆盖，也就是说

{% codeblock lang:java %}
primes = new int[20];
{% endcodeblock %}

将会遵循**"happens-before"**规则并且导致内存栅栏被刷新，但是下面的代码不会这样做

{% codeblock lang:java %}
primes[0] = 10;
primes[1] = 20;
primes[2] = 30;
primes[3] = 40;
{% endcodeblock %}

这表示，如果多个线程修改了单个数组元素，比如保存修改，[volatile修饰符](http://javarevisited.blogspot.com/2012/03/difference-between-transient-and.html)不会为这种修改提供任何happens-before保证。所以，如果你的案例是为单个数组元素提供内存可见性保证，那么volatile不是正确的选择。你必须使用期货同步和线程安全机制来覆盖这种情况，比如[同步](http://www.java67.com/2012/08/5-thread-interview-questions-answers-in.html)关键字，[原子变量](http://javarevisited.blogspot.com//2011/07/java-multi-threading-interview.html)，或者[ReentrantLock](http://javarevisited.blogspot.sg/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)。

<div align=center>[![avatar](https://4.bp.blogspot.com/-aw4100wDmfU/WIFlN8Rr9gI/AAAAAAAAHwk/G7t-OcEYZ0wxqBqGnwYXyfVNNhLfpgstgCLcB/s1600/happens_before-in-java-volatile.png)](https://4.bp.blogspot.com/-aw4100wDmfU/WIFlN8Rr9gI/AAAAAAAAHwk/G7t-OcEYZ0wxqBqGnwYXyfVNNhLfpgstgCLcB/s1600/happens_before-in-java-volatile.png)</div>

有些时候面试官会使用集合而不是数组来问这个问题，也就是他们会问你可不可以将Java的集合变量声明为volatile，比如[ArrayList](http://www.java67.com/2015/06/20-java-arraylist-interview-questions.html)或[HashMap](http://javarevisited.blogspot.sg/2014/11/how-to-loop-hashmap-or-hashtable-in-jsp-example.html)。答案是相同的，当然可以，你可以将Java的任何引用变量指向一个volatile的集合，但是happens-before保证只会提供给引用变量的修改操作，比如你将一个新集合赋值给它。

任何对实际的集合对象做出手修改，比如从ArrayList添加或移除元素都不会引发happens-before保证和内存栅栏刷新。

如果你真的对volatile变量感到好奇以及他们在Java中如何工作，那么我强烈推荐你阅读[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)，这可能是最适合了解Java并发基础的书。

<div align=center>[![avatar](https://2.bp.blogspot.com/-ex9z1dc3xk8/WIFj7sTQ8jI/AAAAAAAAHwY/LUPgjsrKAFYKN12MWGvst4bzLTeTHxNVgCLcB/s320/Java%2BConcurrency%2Bin%2BPractice%2Bstill%2Brelevant%2Bin%2BJava%2B8%2Bera.jpeg)](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)</div>

以上就是所有关于**我们是否可以将java的数组声明为volatile**的内容。是的，你可以将任何数组声明为volatile，但是只有指向数组的引用会被volatile覆盖，对于单个数组元素的修改是不会被覆盖的。

另外，如果你对Java Concurrency in Practice是否还适用于Java 8的时代，那么请别忘记阅读我的博客，[Java Concurrency in Practice还适合Java 8吗](http://javarevisited.blogspot.sg/2016/12/is-java-concurrency-in-practice-still-relevant-in-era-of-java8.html)？这里我解释了为什么它依旧是最适合学习Java并发基础的书。

感觉阅读本文，如果你喜欢这篇文章请分享给你的朋友和同事。如果你有任何建议，修正或任何问题，那么请留下评论，我会抽时间看一看的。

### 延伸阅读
[Java Fundamentals Part 1,2](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=546411875)
[Java Concurrency in Practice](http://www.amazon.com/dp/0321349601/?tag=javamysqlanta-20)
[Applying Concurrency and Multi-threading to Common Java Patterns](http://www.shareasale.com/m-pr.cfm?merchantID=53701&userID=880419&productID=687369751)