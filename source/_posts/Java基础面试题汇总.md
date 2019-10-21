---
title: Java基础面试题汇总
date: 2018-07-01 04:16:31
tags: 面试
categories: 
- 后端
- JAVA
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/java_interview.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p><strong>1. java 中 wait()与sleep() 区别</strong>：<br>答：两者都是暂停当前运行的线程。sleep()是Thread类的方法；wait()是Object类的方法 。调用sleep()，线程不会释放对象锁，其他线程依然无法访问这个对象；调用wait(),线程会释放对象锁，其他线程能访问这个对象。</p>
<p><strong>2. java中代表价格的数据类型用哪个BigDecimal还是double？</strong><br>答：如果需要精确计算的结果，使用BigDecimal类，但是比较消耗内存和性能；否则可以直接使用float或double。</p>
<p><strong>3. a = a + b 与 a += b 的区别？</strong><br>答：内存上分析：如果加法操作的结果比 a 的最大值要大，则 a+b 会出现编译错误；但是 a += b 没问题，+= 隐式的将加操作的结果类型强制转换为持有结果的类型。</p>
<p><strong>4. 我们能在 Switch 中使用 String 吗？</strong><br>答：从 Java 7 开始，我们可以在 switch case 中使用字符串，但这仅仅是一个语法糖。内部实现在 switch 中使用字符串的 hash code。从Java 5开始，Java中引入了枚举类型enum。</p>
<p><strong>5. Java 中 WeakReference（弱引用） 与 SoftReference（软引用）的区别？</strong><br>答： WeakReference 与 SoftReference 都有利于提高 GC 和 内存的效率。 WeakReference ，一旦失去最后一个强引用，就会被 GC 回收；软引用可以延迟到 JVM 内存不足的时候被 GC 回收。</p>
<p><strong>6. Java 中堆和栈有什么区别？</strong><br>答：JVM中堆和栈是不同的内存区域。栈用于保存方法的一些基本类型的变量和对象的引用变量，当超过变量的作用域后，java会自动释放掉为该变量分配的内存空间；堆内存用于存放由new创建的对象，由JVM的GC来管理。</p>
<p><strong>7. “a==b”和”a.equals(b)”有什么区别？</strong><br>答：如果 a 和 b 都是对象，则 a==b 是比较两个对象的引用，只有当 a 和 b 指向的是堆中的同一个对象才会返回 true，而 a.equals(b)是比较逻辑一致，两个对象必须具有相同的 hashCode才返回true，所以通常需要重写equals()方法来提供逻辑一致性的比较。</p>
<p><strong>8. ArrayList 与 LinkedList 的区别？</strong><br>答：都实现了List接口。<br>1.ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。&nbsp;<br>2.对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。&nbsp;<br>3.对于新增和删除操作add和remove，LinedList比较占优势，因为ArrayList要移动数据。</p>
<p><strong>9. String和StringBuilder、StringBuffer的区别？</strong><br>答：String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。<br>而StringBuffer/StringBuilder类表示的字符串对象可以直接进行修改。StringBuilder是Java 5中引入的，它和StringBuffer的方法完全相同，区别在于它是在单线程环境下使用的，因为它的所有方面都没有被synchronized修饰，因此它的效率也比StringBuffer要高。<br>在线程安全上，StringBuilder是线程不安全的，而StringBuffer是线程安全的。</p>
<p><br><strong>10. 抽象类（abstract class）和接口（interface）有什么异同？</strong><br>答：抽象类和接口都不能够实例化，但可以定义抽象类和接口类型的引用。一个类如果继承了某个抽象类或者实现了某个接口都需要对其中的抽象方法全部进行实现，否则该类仍然需要被声明为抽象类。接口比抽象类更加抽象，因为抽象类中可以定义构造器，可以有抽象方法和具体方法，而接口中不能定义构造器而且其中的方法全部都是抽象方法。抽象类中的成员可以是private、默认、protected、public的，而接口中的成员全都是public的。抽象类中可以定义成员变量，而接口中定义的成员变量实际上都是常量。有抽象方法的类必须被声明为抽象类，而抽象类未必要有抽象方法。</p>
<p><strong>11. 用Java写一个单例类。</strong><br>答：<br>- 饿汉式单例</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> Singleton {
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">private</span><span style="color: #000000;"> Singleton(){}
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> Singleton instance = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Singleton();
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span><span style="color: #000000;"> Singleton getInstance(){
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">return</span><span style="color: #000000;"> instance;
&nbsp;&nbsp;&nbsp;&nbsp;}
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>-懒汉式单例</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> Singleton {
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> Singleton instance = <span style="color: #0000ff;">null</span><span style="color: #000000;">;
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">private</span><span style="color: #000000;"> Singleton() {}
&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">synchronized</span><span style="color: #000000;"> Singleton getInstance(){
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">if</span> (instance == <span style="color: #0000ff;">null</span>) instance ＝ <span style="color: #0000ff;">new</span><span style="color: #000000;"> Singleton();
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="color: #0000ff;">return</span><span style="color: #000000;"> instance;
&nbsp;&nbsp;&nbsp;&nbsp;}
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>