# 第八周
# Algorithm
这周的题目来至牛客网，[地址](https://www.nowcoder.com/questionTerminal/2b101eacfaf641988eae013115015d54)，这是一道概率计算的题，主要锻炼的是将概率的数学上的方法用编程的办法来实现。这题的解法就是用所有牛牛能赢妞妞的牌的组合除以整个牌的组合即可得出答案，下面是代码：
``` java
public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String[] boyHandPoke = "3 5 7".split(" ");
        String[] grielHandPoke = "2 6 8".split(" ");
        int[] pocker = new int[14];
        for (int i = 1; i < 14; i++) {
            pocker[i] = 4;
        }
        int bInt = 0;
        int gInt = 0;
        for (int i = 0; i < 3; i++) {
            int b = Integer.valueOf(boyHandPoke[i]);
            bInt += b;
            pocker[b]--;
            int g = Integer.valueOf(grielHandPoke[i]);
            gInt += g;
            pocker[g]--;
        }

        int count = 0;
        double baseCount = 0;
        // 遍历找寻能赢的情况
        for (int i = 1; i < 14; i++) {
            int pi = pocker[i];
            for (int k = 0; k < pi; k++) {
                // 这个 for 很关键对手也有 k 张牌可以拿
                pocker[i]--;
                for (int j = 1; j < 14; j++) {
                    if (bInt + j > gInt + i) {
                        count += pocker[j];
                    }
                }
                pocker[i] = pi;
            }
        }

        if (count == 0) {
            System.out.println("0.0");
        } else {
            // 所有牌的组合情况
            double base = 45 * 46;
            base = count / base;
            Double temp = Double.valueOf(base);
            char[] cs = temp.toString().toCharArray();
            // 输出结果
            for (int i = 0; i < cs.length; i++) {
                if (i < 6) {
                    System.out.print(cs[i]);
                } else {
                    break;
                }
            }
        }
    }
```

## Review
[因特网的黑暗森林理论](https://onezero.medium.com/the-dark-forest-theory-of-the-internet-7dc3e68a7cb1)
当我们看向外太空时一直没有发现任何高等外星生物，因此总觉得我们是唯一的存在，但奇是并不是，我们没有发现有可能是别的生物故意不想让我们发现罢了，它们为什么沉默呢，那是因为宇宙就像黑暗森林，彼此都身处在危险之中最好的保护措施就是不让外界发现自己的存在。


## Tip
最近在 C# 的开发中要使用到 JSON 的解析，所以搜索到了 [Json.NET](https://www.newtonsoft.com/json) 工具，该工具可以将对象转换成 JSON 也可以将 JSON 转换成对象，然后还在 Visual Code 上找到了两个插件，一个是 Paste JSON as Code 插件，你可以通过该插件用一个 JSON 模板生成出用各种语言实现的对象，还有一个是 Json Editor 它可以将一串 JSON 数据用树形格式表示出来。

## Share
在做 C# 的开发，有用到线程池因此记录以下，C# 中线程池的使用，主要通过[他人的博客中学习到的](https://www.cnblogs.com/sashow/archive/2007/02/08/644679.html)
首先你要有一个线程所要执行的方法
``` C#
public static void MyThreadWork ( object state ) {
    // do some thing
}
```
然后申明一个 `WaitCallback ` 对象，构造该对象时将线程要调用的方法传入
``` C#
public static void Main () {
    WaitCallback waitCallback = new WaitCallback ( MyThreadWork );
}
```
最后使用 `ThreadPool.QueueUserWorkItem()` 来实现线程池的调用
``` C#
public static void Main () {
    WaitCallback waitCallback = new WaitCallback ( MyThreadWork );
    ThreadPool.QueueUserWorkItem ( waitCallback, "第一个线程" );
}
```