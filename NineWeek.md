# 第九周
## Algorithm
### [单词拆分](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/275/string/1138/)
给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。
做这道题的思路是将字典里的词根据字母首部进行聚类，然后取字符上开头的第一个字母到聚类中查找对应的单词，若有匹配的单词就将字符串分割到下一个单词的匹配，若没有匹配的单词了则表明该字符不能被字典里的词进行分割
代码
``` java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        char[] cas = s.toCharArray();
        int vernier = 0;
        // 对单词进行聚类
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
        return wordBreakFind(vernier, len, cas, map);
    }

    public boolean wordBreakFind(int vernier, int len, char[] cas, Map<Character, List<String>> map) {
        if (vernier >= len) {
            // 表明完全可以被字典里的词进行分割
            return true;
        }
        char letter = cas[vernier];
        List<String> ls = map.get(letter);
        if (ls == null) {
            // 没有该字母开头的词，立刻返回
            return false;
        }
        for (String s : ls) {
            // 对所有该字符开头词进行遍历
            char[] letChar = s.toCharArray();
            int i;
            for (i = vernier; (i + letChar.length - 1) < len; i+=letChar.length) {
                int k = i;
                // 字典里的词能否在字符串中找出，如果能找到，看是否可以按该单词连续分割
                for (int j = 0; j < letChar.length; j++, k++) {
                    if (cas[k] != letChar[j]){
                        break;
                    }
                }
                if ((k - i) ！= letChar.length){
                    // 单词不匹配退出
                    break;
                }
            }
            if (i ！= vernier) {
                // 单词开头有变化需要进行下一个单词的匹配查找
                if(wordBreakFind(i, len, cas, map)) { 
                    // 表示字典里的词可以完全分割该字符串            
                    return true;
                }
            }
        }
        // 该字符开头的词无法分割
        return false;
    }
}
```
## Review
[Learning the Dynamic Programming through GeeksforGeeks](https://www.geeksforgeeks.org/dynamic-programming/#mediumProblems)
通过 GeeksforGeeks 学习动态规划，在 GeeksforGeeks 上介绍说动态规划是作为递归编程的一种优化，通过存储子问题的答案，来避免重复的计算。典型的例子是斐波那契数问题：
例如求解第 n 位斐波那契数用递归的方式可以写成
``` java
int fib(n) {
    if (n <= 1) {
        return n;
    }
    return fib(n-1) + fib(n-2);
}
```
通过动态规划求解写成
``` java
int fib(n) {
    if (n <= 1) {
        return n;
    }
    int[] f = new int[n + 1];
    f[0] = 0;
    f[1] = 1;
    for (int i = 2; i <= n; i++) {
        f[i] = f[i - 1] + f[i - 2];
    }
    return f[n];
}
```
### 基本概念
#### [Overlapping Subproblems Property in Dynamic Programming | DP-1](https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/)
一个复杂的问题可以通过拆分成一个个子问题来进行求解的话那么该问题就可以通过动态编程解决，这些子问题又称为重叠子问题（overlapping subproblems）或最优子结构（Optimal substructure）
就像分治，动态对各个子问题的结果进行合并。动态规划主要是重复使用相同子问题的结果。也就是说下一个子问题的求解是需要前一个子问题的结果（overlapping subproblems），在求解出每一个子问题的结果后，将结果存入到结果集中以避免重复计算。
有两种不同的方式进行结果集的处理
1. Memoization (Top Down)：通过创建一个映射表结构，对子问题做为键，子问题的结果为值。每次计算前先到表中查询，若碰到相同的问题直接放回值，否则就进行计算，后将结果和问题保存到表中
2. Tabulation (Bottom Up)：将问题构建成一串连续的子问题求解过程，一个子问题计算后将结果放入到结果表中下一个子问题求时直接从表中取出结果在进行计算

Memoization 于 Tabulation 的区别在于 Memoization 中存储的是子问题和子问题的结果，在求解出问题时可能会遇到很多相同的子问题，因此将其缓存起来，但 Tabulation 在求解问题时是像链条一样一个接着一个的解决出来的，下一个子问题的求解是直接使用上一个子问题的答案的。

## Tip
[Linux 用户和用户组管理](https://www.runoob.com/linux/linux-user-manage.html)
Linux 是一个多用户多任务的分时操作系统，每个用户账号都拥有一个唯一的用户名和各自的口令。
### Linux 系统用户账号的管理
添加用户账号就是在系统中创建一个新账号，然后为新账号分配用户号、用户组、主目录和登陆 Shell 等资源。刚添加的账号是被锁定的并且无法使用。

添加新的用户账号命令：
例1：
``` shell
# useradd –d /usr/sam -m sam
```
此命令创建了一个用户sam，其中-d和-m选项用来为登录名sam产生一个主目录/usr/sam（/usr为默认的用户主目录所在的父目录）。
例2：
``` shell
# useradd -s /bin/sh -g group –G adm,root gem
```
此命令新建了一个用户gem，该用户的登录Shell是 /bin/sh，它属于group用户组，同时又属于adm和root用户组，其中group用户组是其主组。
这里可能新建组：#groupadd group及groupadd adm
增加用户账号就是在/etc/passwd文件中为新用户增加一条记录，同时更新其他系统文件如/etc/shadow, /etc/group等。

删除账号：
删除账号就是要将 /etc/passwd 等系统文件中的该用户记录删除，必要时还删除用户的主目录。

例1：
``` shell
# userdel -r sam
```
此命令删除用户sam在系统文件中（主要是/etc/passwd, /etc/shadow, /etc/group等）的记录，同时删除用户的主目录。

修改账号：
修改用户账号就是根据实际情况更改用户的有关属性，如用户号、主目录、用户组、登录Shell等。

例1：
``` shell
# usermod -s /bin/ksh -d /home/z –g developer sam
```
此命令将用户sam的登录Shell修改为ksh，主目录改为/home/z，用户组改为developer。
### 用户口令的管理
用户在刚创建时是没有口令的，但会被系统锁定并无法使用，必须为用户设定口令后才可以使用，即使指定空口令

例1：
``` shell
$ passwd 
Old password:****** 
New password:******* 
Re-enter new password:*******
```
设置当前用户的口令

例2：
``` shell
# passwd sam 
New password:******* 
Re-enter new password:*******
```
若是 root 用户可以设置任何用户的口令

例3：
``` shell
# passwd -d sam
```
删除 sam 用户的口令，再次登陆 sam 用户后系统不会在询问口令

例4：
``` shell
# passwd -l sam
```
用于锁定 sam 用户，使其不能登陆

### 与用户账号有关的系统文件
与用户和用户组相关的信息都存放在一些系统文件中，这些文件包括 /etc/passwd, /etc/shadow, /etc/group 。
/etc/passwd 文件是用户管理工作涉及的最重要的一个文件。Linux 系统中的每个用户都在 /etc/passwd 文件中有一个对应的记录行，它记录了这个用户的一些基本属性。
/etc/passwd
```
用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
```

由于/etc/passwd文件是所有用户都可读的，如果用户的密码太简单或规律比较明显的话，一台普通的计算机就能够很容易地将它破解，因此对安全性要求较高的Linux系统都把加密后的口令字分离出来，单独存放在一个文件中，这个文件是/etc/shadow文件。
/etc/shadow
```
登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志
```

用户组的所有信息都存放在/etc/group文件中。
/etc/group
```
组名:口令:组标识号:组内用户列表
```
## Share
### 网页静态化技术
在多用户高并发的访问网站时，可以使用网页静态化技术降低后台数据库的压力。网页静态化技术于数据缓存技术有些许相似，它们都是为了缓解在高并发下后台服务器数据访问的压力，但于缓存技术不同的是静态页面并不是对局部数据的缓存而是将大部分不变的数据缓存起来。另外另外网页静态化还有利于SEO（搜索引擎优化），并且可以利用 Nginx 来承载更高的并发

### Freemarker
Freemarker 是网页静态化技术中所用到的一种工具，它是一个模板引擎，它通过模板生成文本输出。[官网](https://freemarker.apache.org)
#### Freemarker 入门 DEMO
Maven 依赖
``` xml
<dependency>
  <groupId>org.freemarker</groupId>
  <artifactId>freemarker</artifactId>
  <version>2.3.28</version>
</dependency>
```
创建模板文件
创建模板统一的文件名格式是 `文件名.ftl`，再写模板时可用 `<#-- xxxx -- >` 写注释， `<#-- xxxx -- >` 于 `<！-- xxxx -- >` 的区别在于，`<#-- xxxx -- >` 不会在输出文件中显示而 `<！-- xxxx -- >` 会显示。
大致模板：
``` html
<html>
<head>
    <title>demo</title>
    <meta charset="utf-8">
</head>
<body>
<#-- 注释 -->
<!-- html注释 -->
Hello ${name},${message}
</body>
</html>
```
在创建好模板后，开始用 java 编写程序生成文件
代码
``` java
public class FreemarkerDemoOne {
    public static void main(String[] args) {
        try {
            // 1.创建一个配置对象
            Configuration configuration = new Configuration(Configuration.getVersion());
            // 2.设置模板所在目录
            configuration.setDirectoryForTemplateLoading(new File("D:\\Springtest\\freemarker.demo\\src\\main\\resources"));
            // 3.设置字符集
            configuration.setDefaultEncoding("utf-8");

            // 4.获取模板对象
            Template template = configuration.getTemplate("test.ftl");

            // 5. 创建数据模型 （可以是对象，也可以是map）
            Map<String, String> map = new HashMap<String, String>(16);
            map.put("name", "Dive");
            map.put("message", "测试文件");

            // 6. 创建一个输出流对象
            Writer out = new FileWriter("F:\\javaVideo\\test.html");
            // 7.输出
            template.process(map, out);
            // 8.关闭输出流
            out.close();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (TemplateException e) {
            e.printStackTrace();
        }
    }
}
```
在模板文件中若该绑定的数据没有值映射到，那么运行时会出现异常，下面是异常信息
```
FTL stack trace ("~" means nesting-related):
	- Failed at: ${message}  [in template "test.ftl" at line 9, column 15]
```

### FTL 指令
写模板指令时会使用到 FTL 指令，通过指令可以让模板产生相应的行为。
#### assign 指令
该指令用于在页面上定义变量
示例：
定义一个字符串
``` 
<#assign linkman="周先生">
联系人：${linkman}
```
定义一个对象
```
<#assign info={"mobile":"13301231212",'address':'北京市昌平区王府街'}>
电话：${info.mobile}  地址：${info.address}
```
#### include 指令
该指令用于模板中在嵌套模板
示例：
```
<#include "file.ftl">
```
在当前模板中嵌套 file.ftl 模板

#### if 指令
于大多数编程语言一样 if 用于条件控制
在 freemarker 的判断中，可以使用 = 也可以使用 == 
示例：
```
<#if success=true>
  你已通过实名认证
<#else>  
  你未通过实名认证
</#if>
```

####  list 指令
java 代码：
``` java
List goodsList=new ArrayList();
Map goods1=new HashMap();
goods1.put("name", "苹果");
goods1.put("price", 5.8);
Map goods2=new HashMap();
goods2.put("name", "香蕉");
goods2.put("price", 2.5);
Map goods3=new HashMap();
goods3.put("name", "橘子");
goods3.put("price", 3.2);
goodsList.add(goods1);
goodsList.add(goods2);
goodsList.add(goods3);
map.put("goodsList", goodsList);
```
指令代码：
```
<#list goodsList as goods>
  ${goods_index+1} 商品名称： ${goods.name} 价格：${goods.price}<br>
</#list>
```
goodsList 表示集合，循环取出数据到 goods 里，使用（循环变量 + _index ）可以得到索引值