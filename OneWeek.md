# 第一周
## Algorithm
[链表的两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

这道题考验的是对链表的遍历，在遍历两个长度不同的链表时，需要注意它们的边界，在计算方面由于是十进制的加法，在考虑进位时可以通过`除 10 `获得进位的值，`余 10` 获得个位的值
以下是代码
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = null;
        ListNode index = null;
        // 在长度不一时，短链表后面的节点用该节点代替
        ListNode zeo = new ListNode(0);
        int carry = 0;
        while (true)
        {
            int a = l1.val;
            int b = l2.val;
            int total = a + b + carry;
            // 得出的个位数需要保存到新的节点中
            ListNode ln = new ListNode(total % 10);
            // 获得进位
            carry = total / 10;
            if (head == null) {
                head = new ListNode(total % 10);
                index = head;
            } else {
                index.next = ln;
                index = ln;
            }
            
            if (l1.next == null && l2.next != null) {
                // l2 比 l1 长的情况
                l2 = l2.next;
                l1 = zeo;
            } else if (l1.next != null && l2.next == null) {
                // l1 比 l2 长的情况
                l1 = l1.next;
                l2 = zeo;
            } else if (l1.next != null && l2.next != null) {
                // l1 和 l2 一样长的情况
                l1 = l1.next;
                l2 = l2.next;
            } else {
                // 遍历完成后退出循环
                break;
            }
        }
        if (carry > 0) {
            // 判断最后一个进位值
            index.next = new ListNode(carry);
        }
        
        return head;
    }
}
```

## Review
[Redis Tutorial](https://www.tutorialspoint.com/redis/redis_overview.htm)

### Redis Overview
Redis 是一个开源的,以`键-值`存储的数据仓库
Redis 有 3 个主要的特点
- Redis 的数据库是在内存上的，只有在做持久化时才会使用到硬盘
- Redis 有丰富的数据类型，供你存储
- Redis 可以将数据复制到任意数量的从属服务器上 注：从属服务器（slaves）

Redis 的优点有
- 存值，取值速度特别快（Exceptionally fast）
- 支持丰富的数据结构
- 操作是原子类型的
- 在多个场景中作为实用工具，例如：可以充当缓存，消息队列

redis 的配置
可以通过 CONFIG 命令来查看和设置 redis 的配置，也可以通过修改配置文件，默认的配置文件位于 redis 应用更目录下，名叫 `redis.conf` 的文件
例子：获取链接的端口，若要查看所有能配置的属性可以通过 `CONFIG GET *` 命令
```shell
127.0.0.1:6379> CONFIG GET PORT
1) "port"
2) "6379"
```
例子：设置日志等级
```shell
redis 127.0.0.1:6379> CONFIG SET loglevel "notice" 
OK 
redis 127.0.0.1:6379> CONFIG GET loglevel  
1) "loglevel" 
2) "notice" 
```

redis 支持 5 种数据类型
- String 
- Hashes
- List
- Sets
- Sorted Sets

###String
String 在 Redis 中是一串连续的字节，它是二进制安全的（什么是二进制安全？）,则意味着它的长度不是由任何的结束符来确定的，你一次可以存储大小不超过 512 MB 的 String 数据
例子如下
```shell
# SET 用于存储一个 String
redis 127.0.0.1:6379> SET name "tutorialspoint" 
OK 
# GET 用于取出操作
redis 127.0.0.1:6379> GET name 
"tutorialspoint"
```

###Hashes
Hashes 是一种键值对集合，这里的键和值都是字符串类型的，redis 可以存储超过 40 亿个键值对
例子如下
```shell
# HMSET 用于存储一个 Hashes
127.0.0.1:6379> HMSET hello 1 one 2 two
OK
# HGET 用于取出操作
127.0.0.1:6379> HGET hello 1
"one"
127.0.0.1:6379> HGET hello 2
"two"
# HGETALL 取出所有
127.0.0.1:6379> HGETALL hello
1) "1"
2) "one"
3) "2"
4) "two"
```

###List
List 中存储的也是字符串，里面的数据按照插入顺序进行排序，你可以从头部或尾部插入一个数据到 List 中，每个 List 最大可以存储 $2^{32} - 1$ 个数据
例子如下
```shell
# lpush 命令用于添加操作
127.0.0.1:6379> lpush testList one
(integer) 1
127.0.0.1:6379> lpush testList two
(integer) 2
127.0.0.1:6379> lpush testList three four
(integer) 4
# lrange 命令用于查看
127.0.0.1:6379> lrange testList 0 10
1) "four"
2) "three"
3) "two"
4) "one"
```

### Sets
Sets 是一个无序的字符串集合，并且不存储重复元素，你可以添加或移除元素，并且对判断一个元素是否在这个集合里所用的时间复杂度是 O(1) ，每个 Set 最大可以存储 $2^{32} - 1$ 个数据
例子如下
```shell
redis 127.0.0.1:6379> sadd tutoriallist redis 
(integer) 1 
redis 127.0.0.1:6379> sadd tutoriallist mongodb 
(integer) 1 
redis 127.0.0.1:6379> sadd tutoriallist rabitmq 
(integer) 1 
redis 127.0.0.1:6379> sadd tutoriallist rabitmq 
(integer) 0 
redis 127.0.0.1:6379> smembers tutoriallist  

1) "rabitmq" 
2) "mongodb" 
3) "redis" 
```

### Sorted Sets
Sorted Sets 与 Sets 类型有些相似，它也不存储重复的元素，不同的地方在于每个存储在 Sorted Sets 里的元素都会关联到一个 score 上，Sorted Sets 根据 score 的大小对元素进行排序（从小到大）。在 Sorted Sets 中元素是不会重复的，当 score 是可以重复的
例子如下
```shell
redis 127.0.0.1:6379> zadd tutoriallist 0 redis 
(integer) 1 
redis 127.0.0.1:6379> zadd tutoriallist 0 mongodb 
(integer) 1 
redis 127.0.0.1:6379> zadd tutoriallist 0 rabitmq 
(integer) 1 
redis 127.0.0.1:6379> zadd tutoriallist 0 rabitmq 
(integer) 0 
redis 127.0.0.1:6379> ZRANGEBYSCORE tutoriallist 0 1000  

1) "redis" 
2) "mongodb" 
3) "rabitmq" 
```

以上就是 redis 的基本知识，redis 是一个在内存上进行操作的`键-值`数据库，它的存取速度很快，并且支持 5 种数据类型的存储，分别是：String、List、Sets、Sorted Sets、Hashes，这些数据类型存储的值都是一连串的字节（字符串）

##Tip
###Java 中的死锁的避免与诊断
在 `Java 并发编程实战`一书中介绍了如何避免与诊断死锁，首先发生死锁的一个重要原因就是多个线程之间获取锁的顺序出现了错误，因此尽量减少潜在的加锁交互的数量，将获取锁时所遵循的协议写入正式的文档并始终遵循这些协议。

显示的使用 Lock 类中的定时 tryLock 功能来代替内置锁机制。当使用内置锁时，只要没有获得锁，线程就会永远等待下去，而 tryLock 可以指定一个超时时限，在等待超过该时限后 tryLock 会返回一个失败信息。

通过 Thread Dump 来分析死锁，这是 JVM 的一个检测线程的工具，JVM 通过搜索等待关系图中的循环来找出死锁，可以通过向 JVM 发送 SIGOUIT 信号，或者在 UNIX 平台中 按下 Ctrl-\ 键，Windows 平台中按下 Ctrl-Break 键来触发这个功能

在外部工具中通过 `jstack -F -l <PID>` 可以查看进程中锁的信息

##Share
分享一篇关于 [spring 事务](https://blog.csdn.net/hjm4702192/article/details/17277669)的文章
