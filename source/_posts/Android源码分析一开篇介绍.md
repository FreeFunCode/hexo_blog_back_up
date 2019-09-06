---
title: Android 源码分析（一） 开篇介绍
date: 2018-06-18 19:47:55
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/readTheFuckingSourceCode.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h1>　　Read the fucking source code !!!</h1>
<h2>　　一.前言：</h2>
<p>　　从事Android开发工作也有好几年，说来惭愧，竟然没有好好去研究下android的源码，有种暴殄天物的感觉。一方面从自己技术提升角度，另一方面也是想给自己一个目标，生活也不至于太茫然。准备花段时间好好研究下android的源码，写博客也算是给自己一个完成目标，不至于半途而废。同时，由于本人技术和语言组织能力有限，对于有些技术点多多少少都有点借鉴源自网络，如果未申明转载地址，可能是由于原文出处无法探究，还请原作者谅解。介绍多多少少都有不足之处，可能浅显，可能有误，可能没有描述清楚，还请谅解，可以给我留言，方便完善，不要给其他查看到的同学产生误解就好了。</p>
<h2>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;二.源码下载地址：</h2>
<p>　　我也是fork别人的网盘，忘记了那篇文章地址了，抱歉！感谢！</p>
<p>　　链接: https://pan.baidu.com/s/15PNt1W4jjxZXM9o-qCC94A 密码: p3td</p>
<h2>　　三.源码文件目录介绍：</h2>
<p>&nbsp;</p>
<table style="width: 966px; height: 679px;" border="1" cellspacing="1" cellpadding="1" align="left">
<tbody>
<tr>
<td>根目录</td>
<td>描述</td>
</tr>
<tr>
<td>abi</td>
<td>abi相关代码，应用程序二进制接口</td>
</tr>
<tr>
<td>bionic</td>
<td>bionic C库</td>
</tr>
<tr>
<td>bootable</td>
<td>启动引导相关代码</td>
</tr>
<tr>
<td>build</td>
<td>存放系统编译规则及generic等基础开发配置包</td>
</tr>
<tr>
<td>cts</td>
<td>Android兼容性测试套件标准</td>
</tr>
<tr>
<td>dalvik</td>
<td>dalvik Java虚拟机</td>
</tr>
<tr>
<td>development</td>
<td>应用程序开发相关代码</td>
</tr>
<tr>
<td>device</td>
<td>设备相关代码</td>
</tr>
<tr>
<td>docs</td>
<td>介绍开源的相关文档</td>
</tr>
<tr>
<td>external</td>
<td>Android使用的一些开源的模组</td>
</tr>
<tr>
<td>frameworks</td>
<td>核心框架——Java及C++语言，是Android应用程序的框架</td>
</tr>
<tr>
<td>gdk</td>
<td>即时通信模块</td>
</tr>
<tr>
<td>hardware</td>
<td>主要是硬件适配层HAL代码</td>
</tr>
<tr>
<td>kernel</td>
<td>Linux的内核文件</td>
</tr>
<tr>
<td>libcore</td>
<td>核心库相关</td>
</tr>
<tr>
<td>libnativehelper &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</td>
<td>是Support functions for Android's class libraries的别名，表示动态库，是实现JNI库的基础</td>
</tr>
<tr>
<td>ndk</td>
<td>ndk相关代码。Android NDK(Android Native Development Kit)是一系列的开发工具，允许程序开发人员在Android应用程序中嵌入C/C++语言编写的非托管代码</td>
</tr>
<tr>
<td>out</td>
<td>编译完成后的代码输出在此目录</td>
</tr>
<tr>
<td>packages</td>
<td>应用程序包</td>
</tr>
<tr>
<td>pdk</td>
<td>Plug Development Kit的缩写，是本地开发套件</td>
</tr>
<tr>
<td>prebuilts &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;</td>
<td>x86和ARM架构下预编译的一些资源</td>
</tr>
<tr>
<td>sdk</td>
<td>SDK及模拟器</td>
</tr>
<tr>
<td>system</td>
<td>文件系统和应用及组件，是用C语言实现的</td>
</tr>
<tr>
<td>tools</td>
<td>工具文件夹</td>
</tr>
<tr>
<td>vendor</td>
<td>厂商定制代码</td>
</tr>
<tr>
<td>Makefile</td>
<td>全局的Makefile</td>
</tr>
</tbody>
</table>
<p><img src="https://images2018.cnblogs.com/blog/612293/201806/612293-20180618190254606-1525545894.png" alt="" width="479" height="760"></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>对于源码文件夹目录详细对应的描述可以参考这边博文：https://blog.csdn.net/norry870420/article/details/54288369</p>
<h2>　　四.最后</h2>
<p>　　本来想说总结，但是想想还不是总结的时候，这只是一个开始。</p>
<p>　　后续的随笔我会从自己工作中常用的几个类入手，慢慢逐步分析，由浅入深来探索android源码。由于功力有限，可能前期会介绍的比较浅显，但是这是一个迭代的过程，每篇文章最后都会经过多次修改，逐步完善。毕竟一口吃不掉一个胖子，坚持坚持坚持。</p>
<p>　　最后推荐个博客：老罗Android之旅：https://blog.csdn.net/Luoshengyang/。</p>
<p>&nbsp;</p>
<p>　　</p>
<p>　　</p>
<p>　　</p>
</div>