# 第四周
## Algorithm

[鸡蛋掉落问题](https://leetcode-cn.com/problems/super-egg-drop/)

这道题的解题用**动态规划**可以较快的解决，但由于没有系统的学习过**动态规划**，因此这题变成学习记录了，这题在 `LeetCode` 有英文版的解题解答，[链接](https://leetcode.com/articles/super-egg-drop/)，下面对里面的内容进行翻译
### 解决方案 1：二叉查找结合动态规划
在该方案中使用的是动态规划来解决问题，因为这个问题可以划分成多个相同的子问题，例如：在 K 个鸡蛋、N 层楼里，假设从第 x 层丢下，鸡蛋要么碎了，要么没碎。鸡蛋碎掉的话鸡蛋个数减一（K - 1），并且楼层就是 X - 1 以下的楼层，此时就变成了 K - 1 个鸡蛋与 X - 1 层楼的子问题了，同样的鸡蛋没碎那么就是 K 个鸡蛋与 N - X 层楼的子问题，基于以上分析可得下面的公式：
$$dp(K,N) = \min_{1 \leq X \leq N} (\max(dp(K-1, X -  1), dp(K, N - X)))$$
通过上面的公式发现 $T_1 = dp(K-1, X -  1)$ 会随着 X 的增大而增大，$T_2 = dp(K, N - X)$ 会随着 X 的增大而减小，得到下面的图片
![avatar](https://leetcode.com/articles/Figures/891/sketch.png)
这样便可通过二分查找来找寻 X 的值
后面还有两个解法，但由于能力有限因此附上他人的解读版本[链接](https://github.com/Shellbye/Shellbye.github.io/issues/42)

## Review
[So You Want to be a Functional Programmer](https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-1-1f15e387e536)
这篇文章主要介绍 Functional Programming 概念
### Learning to Drive
在这一节作者把学习编程比喻成学习开车，在你看着他人写代码的时候感觉很简单，但当你自己上手的时候就会发现有各种各样的问题。这时你需要从一个语言入手不断的练习，从开始的 Hello World 到树的遍历再到各式各样的数据结构的实现，这时当你再学习第二种语言的时候就会比学习第一种语言的时候容易了许多。
### Your First Spaceship
当在地面上飞驰已经满足不了你时，这时就该考虑天上的空间了，一旦这样你先前的经验对于这全新的领域是没有太多帮助的，因此你需要从零开始学习。

## Tip

最近学习 JVM 在出现 `java.lang.OutOfMemoryError` 时可以在 JVM 运行时加上 `-XX:+HeapDumpOnOutOfMemoryError` 这条命令，它可以导出一个 `.hprof` 的文件，这文件记录了 JAVA 执行的信息，因此通过该文件不仅可以检查内存溢出的问题还可以检查线程死锁、锁争用（Lock Contention）、Java进程消耗CPU过高等等问题。这个文件里的内容是二进制存储的直接用文本打开是读取不了信息的，需要用到专门的软件或插件来打开，这里我使用的是 Eclipse 旗下的 `MemoryAnalyzer` 软件

## Share

接着上周的 Java 内存模型于内存管理 [原文链接](https://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java)


上周学习了年轻代、老年代的内存管理以及Stop the World 事件。在年轻代中又分了三块区域一个 Eden 和两个 Survivor，当 Eden 区满后会触发 Minor GC，当对象在经过多次 Minor GC 后任然存活，它便会转移到老年代的内存中。在老年代的内存满后会触发 Major GC 该 GC 会进行整个内存的扫描也就意味着运行时间较长，因此若在 Major GC 运行时多次发生 Stop the World 事件就会影响到应用程序的使用。

下面接着学习

### Java 内存模型之Permanent Generation
Permanent Generation 简称 Perm Gen ，这里包含着 JVM 需要的关于应用程序的元数据，例如在应用程序里用到的类、方法。注意它不是 Java 堆内存的一部分

Perm Gen 由 JVM 根据应用程序在运行时的类填充，Perm Gen 同样包含 Java SE 库中的类和方法。Perm Gen 对象是由 Full GC 来进行回收

### Java 内存模型之方法区
方法区是 Perm Gen 的一部分空间，用于存储类的结构（运行时常量和静态变量）、方法代码及构造器

### Java 内存模型之内存池
内存池是创建在 JVM 内存管理的一个不可变对象池，String 池就是一个很好的例子，内存池可以是属于堆或 Perm Gen，这依赖于 JVM 内存管理的实现

### Java 内存模型之运行时常量池
运行时常量池是每个类在运行时的表示的池。它包含类在运行时的常量和静态方法。运行时常量池是方法区的一部分

### Java 内存模型之 Java 栈内存
Java 栈内存是线程执行时使用的。它们包含的是在方法中获得的引用，以及一些在堆上短生命周期的引用

未完待续。。。

