# 第 10 周
## Algorithm
### [创建 Trie 树](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)
Trie 树一般用于字符串的查找功能里，例如：在给定的字典中，查找某个单词是否在该字典中，这样的场景就可以使用 Trie 树。他将字典中的字符串才分成一个个字符，而后将字符拼接成一颗树，在查找单词是否存在时，子需要找寻树的路径即可。详细的介绍可以看[这里](https://mp.weixin.qq.com/s/Y5_r4C5a9gU0FDtqXD9bkQ)。题目中没有要求删除，因此难度系数降低了一些。构建思想是：更具题目输入的要求每个节点最多有 26 个子节点，为此创建数组。在找寻下一个节点时将字符映射到数组下标即可
下面是代码
``` java
class Trie {

    class Node {
        public boolean isTail;
        public int local;
        public Node[] lowerChar = new Node[26];
    }
    
    private Node nodeRoot;
    
    /** Initialize your data structure here. */
    public Trie() {
        nodeRoot = new Node();
    }
    
    /** Inserts a word into the trie. */
    public void insert(String word) {
        char[] chars = word.toCharArray();
        Node subNode = nodeRoot;
        for (int i = 0; i < chars.length; i++) {
            int index = chars[i] - 97;
            if (subNode.lowerChar[index] == null) {
                subNode.lowerChar[index] = new Node();
            }
            subNode = subNode.lowerChar[index];
        }
        subNode.isTail = true;
    }
    
    /** Returns if the word is in the trie. */
    public boolean search(String word) {
        char[] chars = word.toCharArray();
        Node subNode = nodeRoot;
        for (int i = 0; i < chars.length; i++) {
            int index = chars[i] - 97;
            if (subNode.lowerChar[index] == null) {
                return false;
            }
            subNode = subNode.lowerChar[index];
        }
        return subNode.isTail;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    public boolean startsWith(String prefix) {
        char[] chars = prefix.toCharArray();
        Node subNode = nodeRoot;
        for (int i = 0; i < chars.length; i++) {
            int index = chars[i] - 97;
            if (subNode.lowerChar[index] == null) {
                return false;
            }
            subNode = subNode.lowerChar[index];
        }
        return true;
    }
}
```

## Review
接着通过 GeeksforGeeks 学习动态规划
[Optimal Substructure Property in Dynamic Programming | DP-2](https://www.geeksforgeeks.org/optimal-substructure-property-in-dynamic-programming-dp-2/)
接下来学习解决动态规划问题的 2 个主要属性
1. Overlapping Subproblems（迭代子问题，在第九周已经讲过了）
2. Optimal Substructure （最优子结构）
### Optimal Substructure 
最优子结构就是问题的子问题所可以获得的最优结果
最优子结构的例子：最短路径问题
如果一个节点 x 在从 u 节点到 v 节点的最短路径上，那么在在求最短路径问题可以由 u 到 x 的最短路径及 x 到 v 的最短路径组成，跟具体的例子如[Floyd-Warshall](https://www.geeksforgeeks.org/floyd-warshall-algorithm-dp-16/)

向求最长路径就没有最优子结构，这里的最长路径是非循环的两点之间的最长路径。从 q 到 t 有两个最长路径：q->r->t 和 q->s->t。不同于最短路径，q 到 r 的最长路径并不能组合成 q 到 t 应为 q 到 r 的最长路径是 q->s->t->r

## Tip
[Linux 文件于目录管理](https://www.runoob.com/linux/linux-file-content-manage.html)
绝对路径于相对路径
- 绝对路径：
路径的写法，由根目录 `/` 写起，例如：`/usr/share/doc` 这个目录
- 相对路径：
路径的写法，不是由根目录写起，是由当前目录做参考，例如例如由 /usr/share/doc 要到 /usr/share/man 底下时，可以写成： cd ../man 这就是相对路径的写法

### 处理目录的常用命令
一些常用的目录处理命令
- ls：列出目录
- cd：切换目录
- pwd：显示当前目录
- mkdir：创建一个新的目录
- rmdir：删除一个空的目录
- cp：复制文件或目录
- rm：移除文件或目录
- mv：移动文件或目录，或修改文件于目录的名称

可以使用 `man [命令]` 来查看各个命令的使用文档，如 `man rm`

## share
Java 并发编程
线程安全性

如果当多个线程访问一个可变的状态变量时没有合适的同步，那么程序就会出现错误。有三种方式可以修复这个问题：
- 不在线程之间共享该状态变量（例如，将变量声明在线程私有的栈内存中）
- 将状态变量修改围不可变的变量（String 就有这个属性）
- 在访问状态变量时使用同步（synchronized，信号量，volatile，ReentrantLook 等）

当设计线程安全类的类时，良好的面向对象技术、不可修改性、以及明晰的不变性规范都能起到一定的帮助作用

当多个线程访问某个类时，不管运行时环境采用何种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的同步或协同，这个类都能表现出正确的行为，那么就称这个类是线程安全的。