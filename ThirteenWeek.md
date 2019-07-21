# 第十三周
## Algorithm
[字符串中的第一个唯一字符](https://leetcode-cn.com/explore/interview/card/top-interview-quesitons-in-2018/275/string/1143/)
给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。
思路是通过一个长度为 26 的数组，以下标代表字母，用值记录每个字母在字符串中出现的位置加 1。当同一字母出现第二次时将值设置为 -1，对字符串遍历完成后，然后对对后一个数组进行遍历忽略 -1 值和 0 值之后找出最小值，若为没有改变则返回 -1，其他值就减 1 返回。 
代码如下：
``` java
class Solution {
    public int firstUniqChar(String s) {
        char[] letters = s.toCharArray();
        if (letters.length <= 0) {
            return -1;
        }
        int[] map = new int[26];
        for (int i = 0; i < letters.length; i++) {
            char letter = letters[i];
            int index = letter - 'a';
            if (map[index] == 0) {
                map[index] = i + 1;
            } else if (map[index] == -1) {
                continue;
            } else {
                map[index] = -1;
            }
        }
        int mini = Integer.MAX_VALUE;
        boolean isChange = false;
        for (int i = 0; i < map.length; i++) {
            if (map[i] == -1 || map[i] == 0) {
                continue;
            }
            mini = mini > map[i] ? map[i] : mini;
            isChange = true;
        }
        
        return isChange ? mini - 1 : -1;
    }
}
```
## Review
### Learn about Spring Boot
[Maven Installation](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/getting-started-installing-spring-boot.html)
Spring Boot 使用 `org.springframework.boot` `groupId`。通常，你的 Maven POM 文件继承于 `spring-boot-starter-parent` 项目，并且依赖一个或多个 `Starters` 包。Spring Boot 同样提供 Maven 的 plugin 用以生成可执行的 jar。
多数情况下使用 Spring Boot 时用 `spring-boot-starter-parent` 项目是一个好方式。但某些时候你的项目可能需要继承自不同的父 POM，或你不想使用默认的设置，在这些情况下你可以看[这里](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/using-boot-build-systems.html#using-boot-maven-without-a-parent)
下面是配置例子
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>myproject</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<!-- 继承自默认的 Spring Boot 项目 -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.6.RELEASE</version>
	</parent>

	<!-- 添加一个 WEB 应用所依赖的包 -->
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<!-- 可打包成可执行的jar包 -->
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```


## Tips
### JSP的那些内置对象以及这些对象的作用
- request：封装客户端的请求，其中包含来自GET或POST请求的参数；
- response：封装服务器对客户端的响应；
- pageContext：通过该对象可以获取其他对象；
- session：封装用户会话的对象；
- application：封装服务器运行环境的对象；
- out：输出服务器响应的输出流对象；
- config：Web应用的配置对象；
- page：JSP页面本身（相当于Java程序中的this）；
- exception：封装页面抛出异常的对象。

如果用Servlet来生成网页中的动态内容无疑是非常繁琐的工作，另一方面，所有的文本和HTML标签都是硬编码，即使做出微小的修改，都需要进行重新编译。JSP解决了Servlet的这些问题，它是Servlet很好的补充，可以专门用作为用户呈现视图（View），而Servlet作为控制器（Controller）专门负责处理用户请求并转发或重定向到某个页面。基于Java的Web开发很多都同时使用了Servlet和JSP。JSP页面其实是一个Servlet，能够运行Servlet的服务器（Servlet容器）通常也是JSP容器，可以提供JSP页面的运行环境，Tomcat就是一个Servlet/JSP容器。第一次请求一个JSP页面时，Servlet/JSP容器首先将JSP页面转换成一个JSP页面的实现类，这是一个实现了JspPage接口或其子接口HttpJspPage的Java类。JspPage接口是Servlet的子接口，因此每个JSP页面都是一个Servlet。转换成功后，容器会编译Servlet类，之后容器加载和实例化Java字节码，并执行它通常对Servlet所做的生命周期操作。对同一个JSP页面的后续请求，容器会查看这个JSP页面是否被修改过，如果修改过就会重新转换并重新编译并执行。如果没有则执行内存中已经存在的Servlet实例。


## Share
[三种常见的限流算法](https://www.cnblogs.com/linjiqin/p/9707713.html)