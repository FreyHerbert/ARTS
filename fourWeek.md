# 第四周
## Algorithm
[合并两个有序数组](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/261/before-you-start/1109/)

将两个有序数组合并成一个有序数组，在做这道题时，解题思路一直放在如何从小到大的在数组一上进行操作，从小到大的逐一插入到`数组1`要解决一个问题，那就是当`数组2`小于`数组1`时需要将`数组2`的值替换掉`数组1`的值，但`数组1`被替换的值还需要找地方存储起来。当看来评论后才注意到，还有一种更优雅的解法那就是从大到小的处理，因为在覆盖尾部的数字时，是不用考虑额外的存储的。

下面是代码
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        if (nums2.length < 1) {
            // 边界检测
            return;
        }
        // 尾部指针
        int tailIndex = nums1.length - 1;
        // 两个数组中最大的数的位置指针
        m--;
        n--;
        while (tailIndex >= 0) {
            if (m >=0 && n>=0) {
                if (nums2[n] >= nums1[m]) {
                    nums1[tailIndex] = nums2[n];
                    n--;
                } else {
                    nums1[tailIndex] = nums1[m];
                    m--;
                }
                tailIndex--;
            } else if (n < 0) {
                return;
            } else if (m < 0 && n > -1) {
                // 只剩下 nums2 的数据了直接完全归并到 nums1 中
                for (int i = n; i > -1; i--) {
                    nums1[tailIndex] = nums2[i];
                    tailIndex--;
                }
                return;
            }
        }
    }
}
```

## Review
[Redis Tutorial](https://www.tutorialspoint.com/redis/redis_client_connection.htm)

### Client Connection
`Redis` 连接是基于 `TCP` 连接的使用的是 `Unix` 的 `socket`， 当开启端口后将监听将监听端口接收来至客户端的连接，并且连接具有以下特性：
 - `Redis` 的连接采用的是**多路复用的非阻塞 I/O**
 - 为了确保连接没有延迟，`TCP_NODELAY` 将设置到命令中
 - 一个文件可读事件将被创建，`Redis` 服务将对收集到的连接请求将最新的有效数据快速的读取到 Socket 中

在 `redis.conf` 文件中可以配置客户端的最大连接数， 也可以通过 `config` 命令进行设置，它的默认值是 `10000`
```shell
127.0.0.1:6379> config get maxclients
1) "maxclients"
2) "10000"

127.0.0.1:6379> config set maxclients 100
OK
```
尽管你可以设置该值，但实际能连接的数量还是依赖于操作系统的

### Pipelining
`Redis` 是一个 `TCP` 服务器，并且有**请求/相应**协议，在 `Redis` 中完成一个请求有下面 2 步
 - 客户端发送一个请求到服务器，向 `socket` 读取，通常是在一个阻塞的方法中，等待服务器的相应
 - 服务器处理请求的命令并发送响应回到客户端

Pipelining 的基本含义是，客户端发送多个请求到服务器但并不需要等待服务器响应完所有的请求，当有响应时在逐一返回所有的响应。

例子如下：
```shell
$(echo -en "PING\r\n SET tutorial redis\r\nGET tutorial\r\nINCR 
visitor\r\nINCR visitor\r\nINCR visitor\r\n"; sleep 10) | nc localhost 6379  
+PONG 
+OK 
redis 
:1 
:2 
:3 
```

## Tip
通过[菜鸟教程](https://www.runoob.com/linux/linux-file-attr-permission.html)学习 Linux
### Linux 文件基本属性
对于文件来说，它都有一个特定的所有者，也就是对该文件具有所有权的用户。

同时，在Linux系统中，用户是按组分类的，一个用户属于一个或多个组。

文件所有者以外的用户又可以分为文件所有者的同组用户和其他用户。

因此，Linux系统按文件所有者、文件所有者同组用户和其他用户来规定了不同的文件访问权限。

可以使用 `chgrp` 命令更改文件属组，`chown` 更改文件属主，也可以同时更改文件属组


## Share
这周学习 Java 内存模型于内存管理

了解 Java 内存管理有助于理解 java 垃圾回收器的运行。在 java 内存模型中堆内存被分成了两个部分 Yuang Generation 和 Old Generation，可以叫做年轻代和老年代

### 年轻代的内存管理
在年轻代的空间里存放着新创建的对象，如果一时间突然创建了过多的对象就有可能撑满年轻代的空间，此时会触发垃圾收集器（Minor GC），在年轻代的空间中分为 3 个部分，分别是：Eden 和两个 Survivor 区。
在年轻代中有以下几点需要注意：
 - 新创建对象时对象会存放在 Eden 区
 - 在 Eden 区被撑满时，就会触发 Minor GC 将还活动的对象移动到其中一个 Survivor 空间中
 - 两个 Survivor 区会始终保持有一个空的 Survivor 区
 - 对象在经历了几次 GC 事件后还依然存活的话，它将会移动到老年代的空间中，这个可以通 JVM 设置 （-XX:MaxTenuringThreshold）

### 老年代的内存管理
一个对象在经历了多次的 Minor GC 后还依然存活，它就会被移动到老年代的空间中。当老年代的内存空间满后就会触发垃圾收集器（Major GC），Major GC 的回收时间通常会很长。

### Stop the World 事件
在垃圾收集器运行时导致整个应用程序停止,等待垃圾收集器操作完成的事件就叫做 `Stop the World 事件`

在年轻代运行的 Minor GC 是对生命周期较短的对象进行回收，运行速度很快，为此 Minor GC 产生 `Stop the World 事件` 时对应用程序产生的影响就比较小

Major GC 进行回收时需要运行较长的时间，因为它要检查整个存活的对象，如果是在 Major GC 上发生 `Stop the World 事件` 那么就会影响到应用程序的响应。

垃圾收集器的运行时间依赖于垃圾收集策略，正因如此，就需要对垃圾收集进行监控及调优，以避免应用程序出现响应超时的情况，以及提高程序的响应速度


未完待续。。。
