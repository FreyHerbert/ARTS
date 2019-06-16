# 第十一周

## Algorithm
牛客网的[猜数字](https://www.nowcoder.com/questionTerminal/0a5b316cfe9d4c4ba89c6c57a1ee516e)，一开始分析的是使用动态规划，如第 i 个数是素数那么 dp[i]=dp[i-1]*2，因为素数于前面的所有数没有依赖关系，接下考虑的就是非素数的情况了，对于非素数可能的情况当时想的比较的简单，例如像 i = 6 时 6 为 Y 时 1、 2 和 3 都固定为 Y 了此时只有 4 和 5 是可以随便变化的然后把 dp[5] 的情况看作是 6 为 N 的情况就得出了 dp[5] + (1 << (6-4)) 这个式子。

但实时证明我想的太简单了，首先把 dp[5] 中的情况看作是 i = 6 = N，这是错误的，因为 6 限定的 2、3 的取值，当 2、3 都为 Y 时 6 一定是 Y，当 6 为 N 时 2、3 不能同时为 Y（YN，NY，NN），6 为 N 的情况中 dp[5] 已经完全包含在里面了，同时 dp[5] 还包含了 2、3 都为 Y 的情况这里只能将 6 置为 Y 抵消了 6 为 N 的情况所以式子因该是 dp[i] = dp[i-1]

但这并不能使用在所有的非素数中，像是素数幂次的数就不能直接 dp[i] = dp[i - 1] ，例如像 2、4 这两个数当 4 为 N 时 2 既可以是 N 也可以是 Y 此时就有 NN、YN、YY 这 3 种情况，当有 8、4、2 时情况变成了有 4 种，当 8 是 Y 时 4、2 都得为 Y （YYY）、当 8 为 N 时就看 4 若 4 是 Y 2 也得是 Y （NYY），当 8、4 为 N 时看 2 这既可以时 N （NNNN）也可以是 Y （NNY）。若有 3 和 9 时也是这样表现的；此时发现规律可得 n 的素数及他们的幂次数的组合数的乘积，因为以 2 的幂次的组合于以 3 的幂次的组合互不干扰所以做乘法（3 情况下出现多少次 2 情况下就会跟着出现多少次）
为此代码如下
```
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        if (n < 3) {
            System.out.println(n);
        }
        boolean[] contain = new boolean[n + 1];
        long count = 1;
        long modNumber = 1000000007;
        for (int i = 2; i <= n; i++) {
            if (contain[i]) {
                continue;
            }
            for (int j = i + i; j <= n; j += i) {
                contain[j] = true;
            }
            long total = 0;
            long power = i;
            while (power <= n) {
                total++;
                power *= i;
            }
            count = (count * (total + 1)) % modNumber;
        }
        System.out.println(count);
    }

}
```
## Review
接着通过 GeeksforGeeks 学习动态规划
[How to solve a Dynamic Programming Problem](https://www.geeksforgeeks.org/solve-dynamic-programming-problem/)
### 如何解决一个动态规划问题
动态规划是一种在多项式（Polynomial）时间内解决某些特定问题的技术。这项技术在解决这类问题上比穷举暴力破解方法要快很多。
动态规划解决步骤
1. 确保问题是一个动态规划问题
2. 决定一个最小参数表达式状态
3. 确立状态之间的关联
4. 从上到下或从下往上处理
   
### 如何知道一个问题是动态规划问题？
 - 通常像某些问题中最大化或最小化的数量，或者是在限定的条件或可能性下进行计数的问题
 - 所有的动态编程问题满足子问题重叠某些类型还满足最优子结构

### 决定状态
DP 问题是关于状态和他们的转换。这是重要且基本的一步，因为状态转换依赖于你是如何定义状态选择的。下面，来看看状态这个术语

一个状态可以定义成一个参数集，这个参数集可以唯一识别问题中的一个某些位置或地位。这组参数应尽可能小，以减少状态空间

### 确立状态之间的关联
先看下面一个例子
```
Given 3 numbers {1, 3, 5}, we need to tell
the total number of ways we can form a number 'N' 
using the sum of the given three numbers.
(allowing repetitions and different arrangements).
给 3 个数 {1，3，5}，根据这3个数加出一个指定的和数，解出可行的方法的个数

Total number of ways to form 6 is: 8
用这3个数相加得 6 的方法有 8 种
1+1+1+1+1+1
1+1+1+3
1+1+3+1
1+3+1+1
3+1+1+1
3+3
1+5
5+1
```
让我们动态思考这个问题。 所以，首先，我们决定给定问题的状态。 我们将使用参数n来决定状态，因为它可以唯一地识别任何子问题。 所以，我们的状态dp看起来像状态（n）。 这里，state（n）表示通过使用{1,3,5}作为元素来形成n的排列的总数。 现在，我们需要计算state（n）。
 因为我们只能使用1,3或5来形成给定的数字。 让我们假设我们知道n = 1,2,3,4,5,6的结果; 我们希望知道结果（n = 7）我们只能添加1,3和5.现在我们可以通过以下3种方式获得总数7
 在所有可能的状态组合中加1（n = 6）
 ```
[ (1+1+1+1+1+1) + 1]
[ (1+1+1+3) + 1]
[ (1+1+3+1) + 1]
[ (1+3+1+1) + 1]
[ (3+1+1+1) + 1]
[ (3+3) + 1]
[ (1+5) + 1]
[ (5+1) + 1]
 ```
 在所有可能的状态组合中加3（n = 4）
 ```
[(1+1+1+1) + 3]
[(1+3) + 3]
[(3+1) + 3]
 ```
 将5添加到所有可能的状态组合（n = 2）
 ```
 [ (1+1) + 5]
 ```
 现在，仔细思考并满足自己，上述三个案例涵盖了形成总和7的所有可能方式
 state(7) = state (6) + state (4) + state (2)
or
state(7) = state (7-1) + state (7-3) + state (7-5)
 state(n) = state(n-1) + state(n-3) + state(n-5)
之后代码如下
 ```
// initialize to -1 
int dp[MAXN]; 

// this function returns the number of 
// arrangements to form 'n' 
int solve(int n) 
{ 
// base case 
if (n < 0) 
	return 0; 
if (n == 0) 
	return 1; 

// checking if already calculated 
if (dp[n]!=-1) 
	return dp[n]; 

// storing the result and returning 
return dp[n] = solve(n-1) + solve(n-3) + solve(n-5); 
} 

 ```
## Tips
[Linux 磁盘管理](https://www.runoob.com/linux/linux-filesystem.html)
Linux磁盘管理好坏直接关系到整个系统的性能问题。
Linux磁盘管理常用三个命令为df、du和fdisk。
df：列出文件系统的整体磁盘使用量
du：检查磁盘空间使用量
fdisk：用于磁盘分区
Linux 操作系统的文件数据除了文件实际内容外，通常含有非常多的属性，例如 Linux 操作系统的文件权限（rwx）与文件属性（拥有者、群组、时间参数等）。文件系统通常会将这两部分的数据分别存放在不同的区块，权限与属性存放在 inode 中，至于实际数据则放置到 data block 区块中。另外，还有一个超级区块（superblock）会记录整个文件系统的整体信息，包括 inode 与 block 的总量、使用量、剩余量等。

inode：记录文件的属性，一个文件占用一个 inode，同时记录此文件的数据所在的 block。

在 Linux 中，可以使用 stat 命令查看某个文件的 inode 信息：

## Share
Java 并发编程
### 原子性
竞态条件：在程序并发时由于执行时序不恰当从而出现不正确的结果时，就称这种情况为`竞态条件`；当计算的正确性取决于多个线程的交替执行时，就会产生`竞态条件`，最常见的竞态条件类型就是`先检查后执行`
基于一种可能失效的观察结果来做出判断或者执行某个计算，这种类型的竞态条件称为`先检查后执行`

假定有两个操作 A 和 B ，如果从执行 A 的线程来看，当一个线程执行 B 时，要么将 B 的操作全部执行完，要么完全不执行 B 操作，那么 A 和 B 对彼此来说是原子的。原子操作是指，对于访问同一个状态的所有操作（包括该操作本身）来说，这个操作是一个以原子方式执行的操作

在实际情况中，应尽可能地使用现有的线程安全对象（如：AcomicLong）来管理类的状态。于非线程安全的对象相比，判断线程安全对象的可能状态及其状态转换情况要更为容易，从而也更容易维护和验证线程安全性