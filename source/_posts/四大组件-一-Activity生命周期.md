---
layout: android
title: Android 四大组件 (一) Activity 生命周期
date: 2018-04-11 20:00:39
tags: Activity
categories: 
- Android
- 知识点
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/activity_lifecycle.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>&nbsp;</p>
<p>简单粗暴。</p>
<p>分两块来熟悉activity生命周期：<strong>一.典型生命周期过程;二.异常情况下的生命周期过程。</strong></p>
<p>&nbsp;</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201804/612293-20180411190609661-2068970253.png" alt=""></p>
<p><span style="text-decoration: underline;"><em><strong>一. 典型生命周期过程</strong></em></span></p>
<p style="text-align: left;">Activitiy的生命周期方法主要有七个：onCreate()、onRestart()、onStart()、onResume()、onPause()、onStop()、onDestory()。</p>
<p style="text-align: left;">&nbsp;1. onCreate()</p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; onCreate方法是整个生命周期中唯一必须实现的方法。布局都是在Activity中进行，在该方法中做一些初始化的操作，如通过setContentView设置界面布局的资源，初始化所需要的组件信息等。</p>
<p style="text-align: left;">&nbsp;2. onRestart()</p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp; 当Activitiy被onStop()并且没有被killed时候，调用这个方法进行唤醒，也可以说理解为，当处于非栈顶状态的活动需要再次返回栈顶，展现给用户的时候，触发该方法。该方法优先于onStart()方法。</p>
<p style="text-align: left;">&nbsp;3.onStart()</p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp; 表示Activity正在启动，但是无法与用户进行交互。可以把onStart和onStop看成一对。</p>
<p style="text-align: left;">&nbsp;4.onResume()</p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp; Activity已在前台可见，可与用户交互了。</p>
<p style="text-align: left;"><span style="text-decoration: underline;"><em>&nbsp;&nbsp; onResume方法与onStart的相同点是两者都表示Activity可见，只不过onStart回调时Activity还是后台无法与用户交互，而onResume则已显示在前台，可与用户交互。</em></span></p>
<p style="text-align: left;">&nbsp;5.onPause()</p>
<p style="text-align: left;">&nbsp;&nbsp; 这个方法之后有可能是onResume也有可能是onStop。用于提交未保存发生变化了的持久化数据，及停止动画及其他其他比较消耗CPU的事件，这是为了更好的运行新的activity。当在activity中打开的是一个dialog不完全覆盖这个activity，则activity就会是onPause状态。</p>
<p style="text-align: left;">&nbsp;6.onStop()</p>
<p style="text-align: left;">&nbsp;&nbsp; activity完全看不见的时候，会调用onStop方法。当用户自己退出程序的时候，建议在onStop方法中保存数据。此时Activity不可见，仅在后台运行。同样地，在onStop方法可以做一些资源释放的操作（不能太耗时）。</p>
<p style="text-align: left;">&nbsp;7.onDestroy()</p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp; Activity正在被销毁，也是生命周期最后一个执行的方法，一般我们可以在此方法中做一些回收工作和最终的资源释放。</p>
<p style="text-align: left;">&nbsp;</p>
<p style="text-align: left;"><strong><em><span style="text-decoration: underline;">二、异常情况下的生命周期过程</span></em></strong></p>
<p style="text-align: left;">异常生命周期产生原因一般有这两种：</p>
<p style="text-align: left;"><strong>1.横竖屏切换；</strong></p>
<p style="text-align: left;">&nbsp;&nbsp;&nbsp; Activity处于竖屏状态，如果突然旋转屏幕，由于系统配置发生了变化，在默认的情况下，Activity会被销毁并重新创建。</p>
<p style="text-align: left;">&nbsp;&nbsp; <img src="https://images2018.cnblogs.com/blog/612293/201804/612293-20180411194802808-1639713916.png" alt=""></p>
<p style="text-align: left;">&nbsp;&nbsp; 可以在onSaveInstanceState方法中存储一些数据以便Activity重建之后可以恢复这些数据。onSaveInstanceState和onRestoreInstanceState只有在Activity异常终止时才会被调用的，正常情况是不会调用这两个方法的。</p>
<p style="text-align: left;">&nbsp; 当我们不想Activity在屏幕旋转后导致销毁重建时，可以设置configChange=“orientation”；当SDK版本大于13时，我们还需额外添加一个“screenSize”的值，对于这两个值含义如下：<br>orientation：屏幕方向发生变化，配置该参数可以解决横竖屏切换时，Activity重建问题（API&lt;13）<br>screenSize：当设备旋转时，屏幕尺寸发生变化，API&gt;13后必须配置该参数才可以保证横竖切换不会导致Activity重建。<br>设置了这两个参数后，当横竖屏切换时，Activity不会再重建并且也不会调用之前相关的方法，取而代之的是回调onConfigurationChanged方法.</p>
<p style="text-align: left;"><strong>2.内存不足的时候killed优先级低的activity.</strong></p>
<p style="text-align: left;">&nbsp;&nbsp;android系统中进程优先级由高到低依次是：前台进程，可见进程、服务进程、后台进程、空进程.</p>
</div>