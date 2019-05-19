# 第七周
# Algorithm
[分割回文串](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/275/string/1137/)
给于一个 String 尽可能的将这个字符串分割，分割后的子串是回文串。一开始想到的是使用暴力分割的方法，由于上一周解决了验证回文串的问题，那么用暴力的方式循环每一种组合便可解决该问题了。但还可以使用回溯的方法进行求解，下面是代码
``` java
class Solution {
    public List<List<String>> partition(String s) {
        List<List<String>> res = new ArrayList<List<String>>();
	List<String> list = new ArrayList<>();
	this.helper(s, list, res);
	return res;
    }

    private void helper(String s , List<String> list, List<List<String>> res) {
        int len = s.length();
	if(len == 0) {
	    List<String> tmp = new ArrayList<>(list);
	    res.add(tmp);
	    return;
	}
	for(int i=1;i<=len;i++) {
	    String pre = s.substring(0, i);
	    String post = s.substring(i);
	    if(this.isHuiWen(pre)) {
	        list.add(pre);
		this.helper(post, list, res);
		list.remove(list.size() - 1);
	    }
	}
    }

    private boolean isHuiWen(String str){
        int len = str.length(), halfLen = str.length() / 2;
	for(int i=0;i<halfLen;i++){
	    if(str.charAt(i) != str.charAt(len - i - 1)){
	        return false;
	    }
	}
	return true;
    }
}
```

## Review
接上文 [So You Want to be a Functional Programmer](https://medium.com/@cscalfani/so-you-want-to-be-a-functional-programmer-part-1-1f15e387e536)
### Immutability
讲述了不可变性对编程上所带来的好处
你的程序中访问一个值，那你就仅仅是访问而不是修改它没有例外，同样的在多线程环境下它也不能修改到这个值，而是创建一个新的变量返回回去。
`Immutability creates simpler and safer code.`

## Tip
最近上班的时候碰上了要在 Visual Studio 上进行远程调试，由于前期没有接触过特地学习了

我都是从 Microsoft 上学习，[点击查看](https://docs.microsoft.com/zh-cn/visualstudio/debugger/remote-debugging-csharp?view=vs-2019)

## Share
接着上周的 Java 内存模型于内存管理 [原文链接](https://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java)

上周主要学习了 Java 内存管理中的一些 JVM 堆内存调试参数和 java 内存管理之垃圾收集

下面接着学习

### Java 垃圾收集器的类型
下面是 5 种垃圾收集器的类型供我们在程序中使用
1. Serial GC (-XX:+UseSerialGC)：Serial GC 使用简单的`标记-清扫-压缩` 在年轻代和老年代中运作。它对小型的应用特别有好处
2. Parallel GC (-XX:+UseParallelGC)：它跟 Serial GC 大部分功能相同但它是多线程在年轻代运行，单线程的在老年代运行
3. Parallel Old GC (-XX:+UseParallelOldGC):它跟 Parallel GC 一样只不过它的年轻代和老年代都是多线程运行的
4. Concurrent Mark Sweep (CMS) Collector (-XX:+UseConcMarkSweepGC): 这个垃圾收集器适合在对响应较为敏感的应用上使用，它在触发世界停止的时能更快的恢复
5. G1 Garbage Collector (-XX:+UseG1GC): Java 7 后出现的收集器用于替代 CMS ，他没有年轻代和老年代的概念，他将堆等分成多个空间。

### Java 垃圾收集监视
可以使用 jstat 命令来监视，或者用图形化的 jvisualvm 程序。