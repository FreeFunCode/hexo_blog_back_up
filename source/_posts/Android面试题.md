---
title: Android面试题
date: 2019-08-25 22:31:56
tags: 面试
categories: 
- Android
- 知识点
---
[点击查看原文](https://juejin.im/post/5d6289bcf265da03e83b783c)

&nbsp; &nbsp; &nbsp; &nbsp; 个人列出的一些目前常见的面试题，答案都是比较简单的说了几个关键字，具体的还是自己针对每题去查，去了解，去求证。如果有问题，还请及时指出！

## 1.五大布局

LinearLayout，ReativeLayout，AbsoluteLayout, FrameLayout, TableLayout

## 2.Activity生命周期

onCreate-&gt;onRestart-&gt;onStart-&gt;onResume-&gt;onPause-&gt;onStop-&gt;onDestory

## 3.Fragment生命周期

onAttch-&gt;onCreate-&gt;onCreateView-&gt;onActivityCreate-&gt;onStart-&gt;onResume-&gt;onPause-onStop-&gt;onDestoryView-&gt;onDestory-&gt;onDetach

<!-- more -->

[www.cnblogs.com/fajieyefu/p…](https://link.juejin.im?target=https%3A%2F%2Fwww.cnblogs.com%2Ffajieyefu%2Fp%2F6092465.html)

## 4.view事件传递

dispatchTouchEvent, onIntecepterEvent, onTouchEvent

## 5.Handler通信原理

Handler, Looper, MessageQueue, Message

## 6.APK打包流程

aapt-&gt;aidl-&gt;javac-&gt;dx-&gt;apkBuilder-&gt;javaSigner-&gt;zipAlign

## 7.四大组件

Activity, Service，BroadcastReceiver, ContentProvider

## 8.Service启动方式及生命周期

startService,BindService

startService: onCreate-&gt;onStartCommand-&gt;onStop-&gt;onDestory

bindService: onCreate-&gt;onBind-&gt;onUnbind-&gt;onDestory

## 9.进程的优先级

前台进程，可见进程，服务进程，后台进程，空进程

## 10.GC回收对象的优先级

强引用，软引用，弱引用，虚引用，空引用

## 11.GC回收对象的生命周期

新生期，老年期，永生期

## 12.Activity四种LuncherMode

standard，singleTop，singleTask，singleInstance

## 13.hashMap,hashTable,LinkedHashMap,treeMap区别

hashMap: 非线程安全，写入快，允许key-value为null

hashTable: 线程安全，写入慢，不允许key-value为null,

LinkedHashMap: 遍历速度与数据有关和容量无关，hashMap遍历与容量有关，输出的顺序和输入的相同（队列：先进后出），LRUcache内部实现原理。

treeMap:&nbsp;按自然顺序或自定义顺序遍历键优先使用

## 14.LRUcache

LinkedHashMap

## 15.APP冷启动过程

zygote-&gt;Launcher-&gt;ActivityManagerService-&gt;Application-&gt;mainActivity(Activity创建)-&gt;onCreate-&gt;onStart-&gt;onResume-&gt;(view绘制)onMeassure-&gt;onLayout-&gt;onDraw

## 16.进程间通信

Activity，Service, BroadCastReceiver, ContentProvider, Binder

## 17.线程间通信

AnsyTask , Handler

## 18.三种动画

帧动画，补间动画，属性动画

## 19.Android6.0到9.0版本最大的新特性

6.0：运行时权限

7.0：fileProvider文件读取

8.0：多渠道notification通知

9.0：安装外部来源应用授权

## 20View绘制过程

onMeassure-&gt;onLayout-&gt;onDraw

## 21.设计模式五大原则

单一职责：干一件事

开放关闭：对扩展开放，对修改关闭

里氏替换：父类调用，同样适用于子类

依赖倒置：实现依赖抽象，不要抽象依赖实现具体。

接口隔离：使用多个接口，不要一个接口干所有事

## 22.屏幕适配方案

px=dp*(dpi/160)=dp*density

尺寸（size）限定符 --缺点很多，不建议使用

最小宽度（Smallest-width）限定符

今日头条适配方案,setCustomDensity.非浸入。

[www.cnblogs.com/onelikeone/…](https://link.juejin.im?target=https%3A%2F%2Fwww.cnblogs.com%2Fonelikeone%2Fp%2F9583446.html)

## 23.性能优化的几个方面

布局减少嵌套

代码优化，防止内存泄漏

耗电量方面

apk包瘦身

## 24.序列化Serializable,Parcelable区别

serializable: java，大量产生临时变量，内存使用较大，频繁gc

parcelable: android ，内存较小

## 25.列举几点内存泄漏案例案例以及解决办法

## 原因：长生命周期对象持有短生命周期的引用

Context : Activity-&gt;Application

Handler: static

webView 

imageView

未及时关闭资源对象

[blog.csdn.net/unicorn97/a…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Funicorn97%2Farticle%2Fdetails%2F81009204)

## 26.JVM,DVM,ART,JIT,AOT介绍

jvm：java虚拟机

dvm:&nbsp; Android虚拟机

ART: Android运行时

JIT：及时编译

AOT：预编译

[blog.csdn.net/feather_wch…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Ffeather_wch%2Farticle%2Fdetails%2F79800614)

## 27.sleep,wait区别

sleep: 持有锁，Thread子类

wait：释放锁，Object子类

## 28.五层网络协议

应用层（http/https）

传输层 (TCP/UDP)

网络层（IP）

数据链路层

物理层

## 29.保活的方式

黑色保活：app，sdk相互唤起

白色保活：启动前台service

灰色保活：启动两个相同id的Service,将后启动的Service,Stop掉

[www.jianshu.com/p/63aafe3c1…](https://link.juejin.im?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F63aafe3c12af)

## 30.webView的webViewClient和WebChromeClient区别

WebViewClient：帮助WebView处理各种通知、请求事件。

WebChromeClient：辅助WebView处理Javascript的对话框、网站图标、网站title、加载进度等

[blog.csdn.net/u012810020/…](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fu012810020%2Farticle%2Fdetails%2F51820240)

## 31.APK安装过程

/data/app -&gt;androidManifest-&gt;dex-&gt;DVM cache-&gt;PackageManageService-&gt;BroadcastReceiver

## 32.APK包瘦身

so库，混淆，加固，shrinkResource，减少枚举，图片(webp，.9）

## 33.JVM内部结构

静态方法区，栈，堆

## 34.IntentService,HandlerThread介绍

IntentService是一个基于Service的一个类，用来处理异步的请求，内部是通过HandlerThread实现。

[www.cnblogs.com/chenxibobo/…](https://link.juejin.im?target=https%3A%2F%2Fwww.cnblogs.com%2Fchenxibobo%2Fp%2F6047404.html)

## 35.Android五种存储方式

sharePrefences,&nbsp; sqlLite, contentProvider, 文件，网络

## 36.实现线程的两种方式

implements Runnable

extends Thread

## 37.String,StringBuffer,StringBuilder区别

速度：StringBuilder-&gt;StringBuffer-&gt;String（不可变）

StringBuilder 非线程安全

## 38.Synchronize,Volatile区别

sychronize : 加锁

volatile: 可变，每次实时读取。

## 39.ArrayList，LinkedList区别

arrayList：读取快

linkedList：插入快