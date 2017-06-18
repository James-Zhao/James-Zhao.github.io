---
layout: post
title:  Java String
date:   2017-05-23 22:17:00
---

这篇博客是对String的总结，注意，不是系统地讲解String方方面面，想学String的全部知识，看看Java编程思想的字符串这一章就行，这篇博客只是对String一些细节问题的总结。整篇博客会比较零散，我尽量系统地总结好，而且会持续更新。具体引用别人博客的地方，我会附上链接。

## String, StringBuffer, StringBuilder
### String, StringBuffer, StringBuilder三者的区别
Java字符串处理离不开这三个类。这里总结下这三个类的区别，这里参考了[String、StringBuffer与StringBuilder之间区别](http://www.cnblogs.com/A_ming/archive/2010/04/13/1711395.html)。

#### 执行效率
在执行效率上StringBuilder > StringBuffer > String。但是也有例外，详情见下面的特殊的例子。

#### 可变vs不可变
这点其实可以解释这三者执行效率的不同。主要原因是String是不可变类（有兴趣的同学可以看看String源码，其值是private final char value[]定义的），其他两个类是可变类。每次对String进行改变时，实际上是创建了新的对象，原来的对象会被GC回收掉。例如下面的代码，在执行第二句后，s实际上已经指向了新的String，其值为"abcd1"，而之前的String对象"abcd"由于没有引用指向它，所以后面会被GC回收。
```
String s = "abcd";
s = s+1;
System.out.print(s);// result : abcd1
```
#### 线程安全vs线程不安全
线程安全：String, StringBuffer。

线程不安全：StringBuilder。

#### 特殊的例子
```
String str = “This is only a” + “ simple” + “ test”;
StringBuilder builder = new StringBuilder("This is only a").append(" simple").append(" test");
```
这个例子是对执行效率的补充。这里，生成str对象的速度简直太快了，而这个时候StringBuffer居然速度上根本一点都不占优势。其实这是JVM的一个把戏，实际上：
```
String str = “This is only a” + “ simple” + “test”;
```
其实就是：
```
String str = “This is only a simple test”;
```
所以不需要太多的时间了。但大家这里要注意的是，如果你的字符串是来自另外的String对象的话，速度就没那么快了，譬如：
```
String str2 = “This is only a”;
String str3 = “ simple”;
String str4 = “ test”;
String str1 = str2 +str3 + str4;
```
这时候JVM会规规矩矩的按照原来的方式去做。

### 如何比较StringBuffer字符串内容相等
大家都知道String在比较内容相等时，用equals方法就行，这是因为String重写了Object的equals方法。但是StringBuffer没有重写equals方法，所以不能用equals来比较。

所以我们一般用toString()把StringBuffer转换为String，再利用equals来比较。

### String重载"+"与StringBuilder
在Java中的String类已经重载的"+"。也就是说，字符串可以直接使用"+"进行连接。大家都知道，大量用"+"操作的时候，会产生很多垃圾，所以一般用StringBuilder或者StringBuffer。（这部分参考了Java编程思想的第13章字符串）

而我们在用javap反编译的时候，发现编译时仍然将"+"转换成StringBuilder。因此，我们可以得出结论，在进行字符串连接时，实际上使用的是StringBuilder类的append()方法。

但是编译器不会进行很多优化，每进行一次"+"，就会创建一个StringBuilder对象，虽然Java有垃圾回收器，但这个回收器的工作时间是不定的。如果不断产生这样的垃圾，那么仍然会占用大量的资源。解决这个问题的方法就是在程序中直接使用StringBuilder类来连接字符串。
