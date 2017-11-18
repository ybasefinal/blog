---
title: Java中的Fork/Join框架
date: 2017-11-13 22:27:26
tags:
font-size: 16px;
---
翻译自http://gee.cs.oswego.edu/dl/papers/fj.pdf

# 摘要
这篇论文介绍了一种支持并行编程的Java框架的设计、实现及其性能，通过将问题（递归地）分隔为并行执行的子任务，等待它们执行完成后合并为最终结果的方式解决问题。它的总体设计是Cilk的任务窃取构架的一种变体。主要的实现方法围绕任务队列和工作线程的高效构建及管理来进行。性能测试结果显示对大多数程序带来非常好的并行提速，但同时也显示出潜在可能的提升空间。

# 介绍
Fork/Join并行处于取得优秀并行性能最简单与最高效的设计技术之间。Fork/Join算法是熟悉的分隔-及-攻克算法的并行版本，以其典型形式：
{% codeblock lang:java %}
Result solve(Problem problem) {
    if (problem is small)
        directly solve problem
    else {
        split problem into independent parts
        fork new subtasks to solve each part
        join all subtasks
        compose result from subresults
    }
}
{% endcodeblock %}