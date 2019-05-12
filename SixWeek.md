# 第六周
## Algorithm
[验证回文串](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/275/string/1136/)
给一个字符串判断它是否是回文串，对回文字符串的检测有很多的方式，我自己的解法是首先将字符串转换成字符数组，随后使用一头一尾指针从两个指针处获取字符进行比较，相同头尾就各自前移比较下一个，直到头尾交换或重合就结束比较，比较期间若发现两字符不同证明该字符串不是回文字符串。
条件检测：空字符串，有效字符检测，头尾指针检测
他人解法：大多数人也还是使用上述的解法
```
class Solution {
    public boolean isPalindrome(String s) {
        if (s == null || "".equals(s)) return true;
        char[] charArray = s.toCharArray();
        for (int i = 0,j = charArray.length - 1; i <= j;) {
            char ci = charArray[i];
            char cj = charArray[j];
            if ((ci >= 'a' && ci <= 'z') || (ci>='A' && ci <= 'Z') || (ci >= '0' && ci <= '9')) {
                if (ci >= 'a' && ci <= 'z') {
                    ci += ('A' - 'a');
                }
            } else {
                i++;
                continue;
            }
            
            if ((cj >= 'a' && cj <= 'z') || (cj >='A' && cj <= 'Z') || (cj >= '0' && cj <= '9')) {
                if (cj >= 'a' && cj <= 'z') {
                    cj += ('A' - 'a');
                }
            } else {
                j--;
                continue;
            }
            
            if (ci != cj) {
                return false;
            } else {
                i++;
                j--;
            }
        }
        return true;
    }
}
```

## Review
接上文 [So You Want to be a Functional Programmer](https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-1-1f15e387e536)
### Forget Everything You Know
人们总爱谈论这个话题，但大多没有说到点子上。学习 Functional programming 就像是白手起家一样不断的重新学习一切，转变自己的思维模式，将每一次的思维转变都在你的编程中去实践，那么你将成为一个优秀的 Functional programming 作者举了个例子：
当你驾驶汽车的时候你可能需要倒车将车脱离困境，但当你从驾驶汽车突然转到驾驶的是一架飞船时，你会感觉到困惑因为它遇到前方障碍时无法向后走，此时你就需要转变你的思维飞船是不用后退功能的因为他是在一个三维环境里的当你意识到这一点后你就不会为这二维空间的问题所烦恼。

### Purity
当 Functional 程序员们在谈论洁净的时候，他们谈论的是pure function（简洁实用函数），pure function 是一个函数的使用规范，这个函数只操作传入到函数的行参，下面是个 JavaScrip 的例子：
``` javaScript
var z = 10;
function add(x, y) {
    return x + y;
}
```
在上面的例子中 add 函数并没有关联 z 变量，它只和 x 和 y 打交道，这就是 pure function。 pure function 的一个特性就是不能访问除了输入形参以外的参数的函数
下面是另一个 pure function 的例子：
``` javaScript
function justTen() {
    return 10;
}
```
上面的函数虽然是 pure function 但它的编程意义不大，应该将 `justTen` 设置成一个常量更合适一些，为此一个合格的 pure function 都至少有一个参数
``` javaScript
function addNoReturn(x, y) {
    var z = x + y;
}
```
上面的函数也是 pure function 同样的它的意义不大，因为它并没有返回任何参数，为此一个合格的 pure function 都应该带着返回值
下面作者还讲了一些 pure function 的特性：
1. pure function 在相同的输入上总是产生相同的输出（幂等性）
2. pure function 是没有副作用（Side Effects）的

## Tip
使用 Docke 并在 Docker 上安装 Oracle 数据库
### 学习安装 Docker 
Docker 的入门教程可以参考[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)，安装教程可以通过[ Docker 官网学习](https://docs.docker.com/install/linux/docker-ce/ubuntu/)，安装 Docker 可以在线和离线安装。
在安装完 Docker 后可以通过`阮一峰的网络日志`中的[Docker 微服务教程] (http://www.ruanyifeng.com/blog/2018/02/docker-wordpress-tutorial.html)学习。
### 在 Docker 上安装 Oracle
[教程](http://www.thxopen.com/linux/docker/2019/04/17/install-oracle11g-on-docker.html)

## Share
接着上周的 Java 内存模型于内存管理 [原文链接](https://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java)

上周主要学习了，Java 内存模型中有哪些区域以及的各个区域的职责，java 内存中有 5 个区域 Perm Gen、方法区、内存池、运行时常量池、栈内存。

下面接着学习

### Java 内存管理中的一些 JVM 堆内存调试参数
-Xms：表示设置 JVM 启动时堆内存的大小
-Xmx：表示设置堆内存的最大值
-Xmn：设置年轻代在到达一定的容量后将存活的对象放入老年代
-XX:PermGen：设置 Perm Gen 的内存大小
-XX:MaxPermGen：设置 Perm Gen 的最大内存值
-XX:SurvivorRatio：设置 Eden 区和 Survivo 的比例
-XX:NewRatio：设置老年代年轻代之间的比例

### java 内存管理之垃圾收集
Java 垃圾收集器就是标记并处理无用的对象，并整合空间给新创建的对象分配空间。
垃圾收集器运行在程序的后台，寻找内存中哪些没有被程序使用到的对象，发现后将它们所占用的空间给释放掉。
下面是垃圾收集器被调用时的三个步骤：
1. 标记：在内存中识别出哪些是可以回收的对象，对这些对象进行标记
2. 正常释放内存空间：从内存中移除哪些可以回收的对象，让新创建的对象得以有空间供其使用。
3. 释放并压缩内存空间：由于正常释放会让内存参生大量的碎片空间，因此学要对其压缩提高资源的利用

标记并删除这做法有 2 个问题
1. 标记这一步在不同的阶段所花费的时间各有不同，对于大多数，新创建的对象将很快会变成别的可回收的对象了
2. Secondly objects that are in-use for multiple garbage collection cycle are most likely to be in-use for future cycles too.

基于上面的缺点，java 垃圾收集器就被分成了 年轻代和 老年代。他们对应着 Minor GC 和 Major GC

未完待续。。。
