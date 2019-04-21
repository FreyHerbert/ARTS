# 第三周
## Algorithm
[搜索二维矩阵 II](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/261/before-you-start/1108/)
在一个从横向看从左到右排序，纵向看从上到下排序的二维数组中做元素的查找，在看到有序的数组中找元素第一个想到的就是使用二分查找，因此我写的第一个方法就是逐行用二分查找来做，用 `M` 表示行的个数 `N` 表示列的个数的话，我想此时的时间复杂度因该是 `O(M lg N)` 

之后在看了大家的讨论后发现这个数组可以抽象成一颗二叉查找树，例如将右上角比作树根，比左边的都是比根小的值右边呢都是比根大的值，为此算法可以变成这样
- 当大于目标元素时，列往左移动
- 当小于目标元素时，行往下移动
- 等于时目标元素时，找到元素，方法结束
- 当行和列有一方超出数组边界时，相当于未找到元素，方法结束

基于上面的方法最后的代码如下
```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length == 0 || matrix[0].length == 0)
        {
            // 空数组检查
            return false;
        }
        int row = 0;
        int col = matrix[row].length - 1;
        // 循环条件是行和列都在数组边界内
        while (row < matrix.length && col > -1) {
            if (matrix[row][col] > target) {
                // 大于目标元素，列往左移动
                col--;
            } else if (matrix[row][col] < target) {
                // 小于目标元素，行往下移动
                row++;
            } else {
                return true;
            }
        }
        // 超出数组边界，相当于未找到元素
        return false;
    }
    
}
```

## Review
[Redis Tutorial](https://www.tutorialspoint.com/redis/redis_backup.htm)
上一周主要学习了 Redis 的常用命令，例如在键上可以增删改查，在 String 数据结构上你可以使用 INCR 命令来计数，用 PSETEX 命令设置有效期等等。

这一周接着学习 Redis ，学习它的高级特性
### Backup
Redis 的 **SAVE** 命令可以对当前的 Redis 数据进行备份
例子如下
``` shell
127.0.0.1:6379> SAVE  
OK 
```
该命令将会创建一个 `dump.rdb` 文件，它放在你的 Redis 目录中，该目录路径可以使用 `CONFIG` 命令来擦看
例子如下
``` shell
127.0.0.1:6379> config get dir
1) "dir"
2) "/root/redis-5.0.3"
```
如要恢复 Redis 数据，可以将备份文件 `dump.rdb` 放入到你的 Redis 文件夹下并启动 Redis 服务就行了
Redis 有个 **BGSAVE** 命令，效果跟 **SAVE** 命令一样，只不过 **BGSAVE** 是在后台进行备份的
### Security
在安全性方面 Redis 可以设置访问密码。通过 `CONFIG get requirepass` 命令即可查看密码，默认值是空的，可以通过 `CONFIG set requirepass 'password'` 进行设置，在设置好密码后，执行任何命令前是需要先验证密码的。你可以在连接时加上 `-a password` 来验证，也可以通过 `AUTH` 命令来验证密码
例子如下
```shell
127.0.0.1:6379> AUTH "tutorialspoint" 
OK 
127.0.0.1:6379> SET mykey "Test value" 
OK 
```
### Benchmarks
Redis Benchmark 是用于检测 Redis 在运行 n 个命令时的所表现的性能的工具。
下面是基本语法
``` shell
redis-benchmark [option] [option value] 
```
例子如下
```
redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 100000 -q  

SET: 146198.83 requests per second 
LPUSH: 145560.41 requests per second 
```

## Tip
通过[菜鸟教程](http://www.runoob.com/linux/linux-system-contents.html)学习 Linux
### Linux 系统目录结构
通过 `ls /` 查看根目录
``` shell
[root@localhost ~]# ls /
bin   dev  home  lost+found  mnt  proc  sbin     soft  sys  usr
boot  etc  lib   media       opt  root  selinux  srv   tmp  var
```
bin： 目录里存放着最经常使用的命令
boot： 目录里存放着 Linux 启动时使用的核心文件
dev： 目录里存放着 Linux 的外部设备，在 Linux 中访问设备和访问文件的方式是相同的
etc： 目录里存放系统管理所需的配置文件
home： 用户的主目录
media： linux 自动识别的设备，例如U盘、光驱等等，这些设备会挂载到该目录
opt：安装额外软件所摆放的目录
usr：类似 windows 下的 program files 目录
lost+found：这个目录一般情况下是空的，当系统非法关机后，这里就存放了一些文件。

## Share
这周把上周提到的红黑树给实现出来了，[点击查看代码]()

在实现中需要注意的细节有，插入时首先是将节点置为红色后插入，插入后根据`叔叔节点`颜色以及在`父亲节点`的左侧或是右侧，需要分类处理

删除操作相比于插入要复杂了，首先是处理需要用哪个节点进行替换，如果要删除的节点是有两个孩子的话，需要用它的**后继节点**进行替换，**后继节点**就是第一个比它大的节点，并且所要删除节点的情况有所改变，变成了删除**后继节点**，**后继节点**覆盖原先所要删除的节点。