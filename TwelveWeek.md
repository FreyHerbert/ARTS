# 第十二周

# Algorithm
### [单词拆分 II](https://leetcode-cn.com/problems/word-break-ii/)
该题目的做法是根据第九周的单词拆分基础上做了少许改动得出来的，拆分的整体思想没有变，只不过是将拆分过程进行记录，若拆分成功就整体记录到 List 容器中，同时增加一个数组用来记录无法拆分的点，用以减少计算量。下面是代码
``` java
class Solution {
    public static LinkedList<String> sb = new LinkedList<>();
    public static List<String> breakString = new ArrayList<>(12);
    public static LinkedList<Integer> link = new LinkedList<>();
    public List<String> wordBreak(String s, List<String> wordDict) {
        sb.clear();
        breakString.clear();
        link.clear();
        char[] cas = s.toCharArray();
        int vernier = 0;
        Map<Character, List<String>> map = new HashMap<>();
        for (String st : wordDict) {
            char c = st.charAt(0);
            List<String> ls = map.get(c);
            if (ls == null) {
                ls = new ArrayList<>();
                ls.add(st);
            } else {
                ls.add(st);
            }
            map.put(c, ls);
        }
        int len = cas.length;
        boolean[] isFail = new boolean[len];
        boolean[] isSuccess = new boolean[len];
        wordBreakFind(vernier, len, cas, map, isFail, isSuccess);
        return breakString;
    }
    
    public boolean wordBreakFind(int vernier, int len, char[] cas,
                                 Map<Character, List<String>> map, boolean[] isFail, boolean[] isSuccess) {
        if (vernier >= len) {
            return true;
        }
        if (isFail[vernier]) {
            return false;
        }
        link.push(vernier);
        char letter = cas[vernier];
        List<String> ls = map.get(letter);
        if (ls == null) {
            return false;
        }
        for (String s : ls) {
            char[] letChar = s.toCharArray();
            int i;
            for (i = vernier; (i + letChar.length - 1) < len; i+=letChar.length) {
                int k = i;
                for (int j = 0; j < letChar.length; j++, k++) {
                    if (cas[k] != letChar[j]){
                        break;
                    }
                }
                if ((k - i) == letChar.length) {
                    sb.push(s);
                    i+=letChar.length;
                }
                break;
            }
            if (i == vernier) {
                continue;
            } else {
                if(wordBreakFind(i, len, cas, map, isFail, isSuccess)) {
                    breakString.add(convert2String());
                    setIsSuccess(isSuccess);
                }
                sb.pop();
            }
        }
        Integer index = link.pop();
        if (!isSuccess[index]) {
            isFail[index] = true;
        }
        return false;
    }

    public String convert2String() {
        StringBuilder ss = new StringBuilder();
        boolean isFirst = true;
        for (int i = sb.size() - 1; i >= 0; i--) {
            if (isFirst) {
                ss.append(sb.get(i));
                isFirst = false;
            } else {
                ss.append(" " + sb.get(i));
            }
        }
        return ss.toString();
    }
    
    public boolean[] setIsSuccess(boolean[] isSuccess) {
        for (Integer index:
             link) {
            isSuccess[index] = true;
        }
        return isSuccess;
    }
}
```
## Review
### [Amazon Is Watching](https://onezero.medium.com/amazon-is-watching-d51b20f1668a)
讲诉了亚马逊在2017年可以给用户提供的服务， 像用 Alexa 做语音控制，在亚马逊商城上买东西
指出亚马逊的强大之处，指出亚马逊是一家超有价值的一家公司
但作者觉得那只是假象，它指出亚马逊在不断的侵蚀个人隐私

## Tips
vim 中批量添加注释

方法一 ：块选择模式

批量注释：

Ctrl + v 进入块选择模式，然后移动光标选中你要注释的行，再按大写的 I 进入行首插入模式输入注释符号如 // 或 #，输入完毕之后，按两下 ESC，Vim 会自动将你选中的所有行首都加上注释，保存退出完成注释。

取消注释：

Ctrl + v 进入块选择模式，选中你要删除的行首的注释符号，注意 // 要选中两个，选好之后按 d 即可删除注释，ESC 保存退出。

方法二: 替换命令

批量注释。

使用下面命令在指定的行首添加注释。

使用名命令格式： :起始行号,结束行号s/^/注释符/g（注意冒号）。

取消注释：

使用名命令格式： :起始行号,结束行号s/^注释符//g（注意冒号）。

例子：

1、在 10 - 20 行添加 // 注释
```
:10,20s#^#//#g
```
2、在 10 - 20 行删除 // 注释
```
:10,20s#^//##g
```
3、在 10 - 20 行添加 # 注释
```
:10,20s/^/#/g
```
4、在 10 - 20 行删除 # 注释
```
:10,20s/#//g
```
## Share
Java 并发编程
### 加锁机制
要保持状态的一致性，就需要在单个原子操作中更新所有相关的状态变量
内置锁
获得内置锁的唯一途径就是进入由这个锁保护的同步代码块或方法
当线程 A 尝试获取一个由线程 B 持有的锁时，线程 A 必须等待或者阻塞，直到线程 B 释放这个锁，如果 B 永远不释放锁， A 就永远的等待下去
任何一个执行同步代码块的线程，都不可能看到有其他线程正在执行由同一个锁保护的同步代码块
内置锁是可以重入的，这意味着获取锁的操作的粒度是`线程`而不是`调用`
