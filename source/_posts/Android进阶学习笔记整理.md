---
title: Android进阶学习笔记整理
date: 2018-07-22 16:27:43
tags: 安卓源码
categories: 安卓源码
top: 1
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/9350418.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <div>
<h2>一．android系统架构图及各层介绍</h2>
<p style="text-align: center;"><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180722160006222-1427704878.jpg" alt=""></p>
<strong>1. 应用层</strong>：应用是用java语言编写的运行在虚拟机上的程序，比如通讯录，日历,电话，短信，浏览器等。</div>
<div><br><strong>2. 应用框架层</strong>：这一层是编写Google发布的核心应用时所使用的API框架，开发人员同样可以使用这些框架来开发自己的应用，这样便简化了程序开发的结构设计，但是必须要遵守其框架的开发原则。应用程序框架层包括活动管理器、窗口管理器、内容提供者、视图系统、包管理器、电话管理器、资源管理器、位置管理器、通知管理器和XMPP服务十个部分。</div>
<div><br><strong>3. 统运行库（C/C++库以及Android运行库）层</strong>：当使用Android应用框架时，Android系统会通过一些C/C++库来支持我们使用的各个组件，使其更好的为我们服务，比如其中的SQLite（关系数据库），Webkit，chromium（Web浏览器引擎），Dalivk,ART(Android Runtime)。</div>
<div><br><strong>4.Linux内核层</strong>：Android的核心系统服务基于Linux内核，如安全性、内存管理、进程管理、用户权限管理、网络协议栈和驱动模型等都依赖于该内核，比如Binder IPC(Internet Process Connection进程间通信)驱动，android的一个特殊驱动程序，具有单独的设备节点，提供进程间通信的功能。</div>
<div>&nbsp; &nbsp; &nbsp; &nbsp; </div>
<div>&nbsp; &nbsp; &nbsp; &nbsp; 若是从事Android应用开发，那应该研究Android的应用框架层和应用程序层；若是从事Android系统开发，那应该研究Android的系统库和Android运行时；若是从事Android驱动开发，那应该研究Android的Linux内核。找准定位，事倍功半。</div>
<div>
<h2>二. 随笔整理（导读）</h2>
<h3>Android 应用层：</h3>







</div>
<div><ol>
<li><a id="homepage1_HomePageDays_DaysList_ctl05_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/activity_lifecycle.html" target="_blank">Android 四大组件 (一) Activity 生命周期</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl02_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/android_service.html" target="_blank">Android 四大组件 (二) Service 使用</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/BroadcastReceiver.html" target="_blank">Android 四大组件 (三) BroadcastReceiver 介绍</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/ContentProvider.html" target="_blank">Android 四大组件 (四) ContentProvider介绍</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl02_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/fragment.html" target="_blank">Android 四大组件 (五) 第五大组件 - Fragment 使用</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl04_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/android8_new_features.html" target="_blank">Android 8.0新特性介绍以及注意事项</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/aidl.html" target="_blank">AIDL介绍以及简单使用</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/viewpager.html" target="_blank">ViewPager 相关使用</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_3" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/Serializable_Parcelable.html" target="_blank">Serializable和Parcelable之序列化</a></li>





</ol></div>
<h3><br>Android 框架层：</h3>
<div><ol>
<li>&nbsp;<a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/readTheFuckingSourceCode.html" target="_blank">Android 源码分析（一） 开篇介绍</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl04_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/startActivity.html" target="_blank">Android 源码分析（二） Activity 启动分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl04_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/startService.html" target="_blank">Android 源码分析（三） Service 启动分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_4" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/handler.html" target="_blank">Android 源码分析（四） Handler 异步消息机制</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_3" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/zygote.html" target="_blank">Android 源码分析（五） Zygote 进程</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_2" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/SystemServer.html" target="_blank">Android 源码分析（六） SystemServer 进程</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/Launcher.html" target="_blank">Android 源码分析（七） Launcher 桌面程序启动分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl01_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/Launcher_app.html" target="_blank">Android 源码分析（八） Launcher 桌面启动App过程</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/init.html" target="_blank">Android 源码分析（九） Init 启动分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl05_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/dalvik.html" target="_blank">Android 源码分析（十） Dalvik 虚拟机创建过程</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl03_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/touchEvent.html" target="_blank">Android 源码分析（十一） 事件传递机制</a><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_2" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/ServiceManager.html">Android源码分析（十二）ServiceManager服务分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_2" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/ServiceManager.html" target="_blank">Android源码分析（十二）ServiceManager服务分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/ActivityManagerService.html" target="_blank">Android源码分析（十三）ActivityManagerService服务分析</a></li>
<li><a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/PackageManagerService.html" target="_blank">Android源码分析（十四）PackageManagerService服务分析</a></li>





</ol></div>
<h2>三.学习资料</h2>
<p>　　<strong>1.Android源码</strong>&nbsp;<a href="https://pan.baidu.com/s/15PNt1W4jjxZXM9o-qCC94A" target="_blank">https://pan.baidu.com/s/15PNt1W4jjxZXM9o-qCC94A</a> 密码：p3td</p>
<p>　　<strong>2.Android 6.0 离线API</strong> <a href="https://pan.baidu.com/s/17E01X32lHOrz0FN_QlXmwQ%20" target="_blank">https://pan.baidu.com/s/17E01X32lHOrz0FN_QlXmwQ </a>密码: styi</p>
<p>　　<strong>3.探索Android FrameWork底层开发视频</strong> <a href="https://pan.baidu.com/s/14BiZNm2C362afq3K-c3AJA" target="_blank">https://pan.baidu.com/s/14BiZNm2C362afq3K-c3AJA</a> 密码: i7yd</p>
<div>&nbsp; &nbsp; &nbsp; &nbsp;</div>
<div>&nbsp; &nbsp; &nbsp;&nbsp; 在学习过程中借鉴也翻阅了很多网上大牛的一些文章，加深自己的理解，如果文中未注明出处原文，还请原作者谅解，因为实在是找不到原文了。这里我推荐两个对Android 框架层研究比较透彻的两位大牛的bolg，他们帮助我加深了很多概念的理解。感谢他们。<br>　　《深入理解Android系列》丛书的作者： <a href="http://www.cnblogs.com/innost" target="_blank">http://www.cnblogs.com/innost</a><br>　　《Android系统源代码情景分析》作者：<a href="https://blog.csdn.net/Luoshengyang/" target="_blank">https://blog.csdn.net/Luoshengyang/</a></div>
<div>&nbsp; &nbsp; &nbsp; &nbsp; 我的系列随笔只是作为一个自己的学习笔记，理解程度无法与两位大牛著作对比，但是可以作为读者或者自己初步探索Android源码和理解其实现原理的第一步。</div>
<div>&nbsp; &nbsp; &nbsp; &nbsp; </div>
<div>&nbsp; &nbsp; &nbsp; &nbsp; 在工作中，会发现，其实很多东西，我们都会，但是就是讲不出来。学计算机出生，保持严谨性是必须的，有时候正因为时刻的严谨性，让我们不敢轻易对一些概念东西给出自己观点，怕说错，归根到底是对其实现原理理解不够透彻，让我们畏手畏脚的进行总结。“大胆假设，小心验证” 我想这句话可以作为我们学习技术的一个参考。</div>
<div>&nbsp;</div>
<div>&nbsp;</div>
<div><span style="text-decoration: underline;">附上android知识体系图</span>：</div>
<div style="text-align: center;">　　<img src="https://images2018.cnblogs.com/blog/612293/201808/612293-20180805222343312-808261016.jpg" alt=""></div>
</div>