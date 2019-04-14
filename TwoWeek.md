# 第二周
## Algorithm
[求众数](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/261/before-you-start/1107/)
这是一道简单难度的题目，就是在一个整数数组中找出占了一半以上空间的元素，较容易的解题思路是在数组中不同的元素给个相应的计数器记录元素的个数，当某个元素的数量大于数组长度的一半时，该元素就是所要寻找的答案。
在上面的解题思路中给元素添加计数器是比较碍事的，那有没有更便利的方法来代替呢？答案是：有的，首先将数组里的每一个元素看成两种状态，**该元素是众数**,**该元素不是众数**，由于**众数元素**大于**非众数元素**，因此当出现一个**非众数元素**时就抵消一个**众数元素**，就这样通过抵消的方式最后得出的元素就是**众数元素**。具体的操作是这样的，首先设置一个计数器 `count = 1` 然后假设数组的第一个元素是**众数元素**，此时 `count + 1` 接着开始逐一遍历数组，在遇到元素相同时 `count + 1` （表示**众数元素**个数 +1），元素不相同时 `count - 1` （表示**非众数元素**抵消**众数元素**），当 `count` 为 0 时说明我们假设的元素很可能是错的，此时可以抛弃掉先前的假设，为此设下一个元素是**众数元素**接着先前步骤，最后 `count` 不为 0 时所对应的元素就是**众数元素**了
具体代码如下
```java
class Solution {
    public int majorityElement(int[] nums) {
        int count = 1, jorityElement = nums[0];
        if (nums.length < 2)
        {
            return nums[0];
        }
        int len = nums.length;
        for (int i = 1; i < len; i++)
        {
            if (count == 0){
                // 前面的假设有可能是错的，重置假设
                jorityElement = nums[i];
            }
            
            if (jorityElement == nums[i]) {
                count++;
            } else {
                count--;
            }
            
            if (count > (len / 2)) {
                // 根据题目特性，当元素个数多于 n/2 时，该元素为众数
                return jorityElement;
            }
        }
        return jorityElement;
    }
}
```

## Review
[Redis Tutorial](https://www.tutorialspoint.com/redis/redis_commands.htm)
接着上一周继续学习 `Redis`，上一周主要了解了 `Redis` 的基本概况，它的优缺点，它的能存储的数据结构，下面开始学习它的命令

Redis 的命令是作用于 Redis 服务器上的，在服务器上运行命令需要使用 Redis 客户端，下面是基本的客户端语法
```shell
$redis-cli 
```
在开启终端后键入上面的命令 `redis-cli` ,这将会链接到你本地的 redis 服务，接着你可以执行 **PING** 命令来检查服务是否在运行。
```shell
$redis-cli 
redis 127.0.0.1:6379> 
redis 127.0.0.1:6379> PING  
PONG
```
若要远程连接到 Redis 服务可以使用以下语法
```shell
$ redis-cli -h host -p port -a password
```
例如，远程连接到 192.168.1.2 上 端口为 6379 密码为 mypass 的 Redis 服务
```shell
$redis-cli -h 192.168.1.2 -p 6379 -a "mypass" 
redis 192.168.1.2:6379> 
redis 192.168.1.2:6379> PING  
PONG
```

Redis 中对键管理的语法是下面这样的
```shell
redis 127.0.0.1:6379> COMMAND KEY_NAME
# 例如删除一个已存在的 key 
redis 127.0.0.1:6379> DEL tutorialspoint
```
上面的例子中 **DEL** 就是命令，**tutorialspoint** 就是键，通过这样的方式不仅可以删除键，还可以判断键是否存在，设置键的有效期等等操作

## Tip
通过[菜鸟教程](http://www.runoob.com/linux/linux-system-boot.html)学习 Linux 
### Linux 系统的启动过程
在教程中介绍了 Linux 启动的 5 个阶段
- 内核引导
- 运行 init
- 系统初始化
- 建立终端
- 用户登陆系统
  
内核引导用于启动硬件设备交给操作系统管理，读取 /boot 目录下的文件

运行 init 进程，init 进程是所有进程的起点，之后有运行级别的概念，Linux 允许为不同的场合，分配不同的开机启动程序，这就叫做**运行级别**，Linux 有 7 个运行级别
- 运行级别0：系统停机状态，系统默认运行级别不能设为0，否则不能正常启动
- 运行级别1：单用户工作状态，root权限，用于系统维护，禁止远程登陆
- 运行级别2：多用户状态(没有NFS)
- 运行级别3：完全的多用户状态(有NFS)，登陆后进入控制台命令行模式
- 运行级别4：系统未使用，保留
- 运行级别5：X11控制台，登陆后进入图形GUI模式
- 运行级别6：系统正常关闭并重启，默认运行级别不能设为6，否则不能正常启动

在系统初始化阶段主要完成激活交换分区、检查磁盘、加载硬件模块等任务

Linux 会建立 6 个终端共用户登陆

用户登陆的方式有三种：
- 命令行登陆
- ssh 登陆
- 图形界面登陆
  
在为安全考虑时可以通过配置 /etc/securetty 中的内容来指定哪些终端可以登陆到 root 用户，如果不存在该文件那么 root 用户可以在任何终端上登陆

图形模式于文字模式的切换可以通过 `Ctrl + Alt + F1 ~ F6` 来进行操作

## Share
最近重温了以下红黑树
首先红黑树具有如下性质：
- 每个节点或是红色或是黑色
- 根节点是黑色的
- 如果节点是红色的那么它的两个子节点一定是黑色的
- 对每个节点，从该节点到其所有后代叶节点的简单路径上，黑色节点的个数是一样的

一颗红黑树的高至多为 2lg(n+1) 在对树进行插入、删除操作时运行时间为 O(lg n) 
具体的实现细节可以参考《算法导论 3rd》中的红黑树一节，下周给出用 java 实现一颗红黑树。