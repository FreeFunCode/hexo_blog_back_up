---
layout: android
title: Android 四大组件 (三) BroadcastReceiver 介绍
date: 2018-05-16 12:33:39
tags: BroadcastReceiver
categories: Android
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/BroadcastReceiver.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一. BroadcastReceiver 使用场景</h2>
<p>　　Broadcast主要是用作消息通信（专业术语叫IPC），Android的IPC基本都是binder来实现，那么这里的BroadcastReceiver其实也是对binder的一个封装，方便上层调用。开发过程中遇到的单进程多线程通信的场景比较多，所以这种情况下使用Broadcast并不是最佳选择，很多人会自己实现一套 Observer 或者使用EventBus等第三方库来完成该功能，不可否认，他们在这种场合下不论是效率还是灵活性方面都更具优势。但是，BroadcastReceiver既然能够在Android四大组件中占有一席之地，自然也有它独有的优势，第一个是系统相关事件的监听，比如开机启动，网络连接，电量变化等，第二个是多进程通信，这些是Observer 或者EventBus很难办到的。所以BroadcastReceiver的使用需要看具体的使用场景，像单进程多线程这种场景，就不建议用BroadcastReceiver了，有种杀鸡用牛刀的赶脚，使用Observer 或者EventBus更适合；但是对于需要监听系统广播事件的场合，比方说现在很多进程保活机制里面就用到了一些系统广播的监听，就正是BroadcastReceiver大展拳脚的时候了。</p>
<h2>二.BroadcastReceiver 系统广播</h2>
<p>Android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广播。每个广播都有特定的Intent - Filter（包括具体的action），Android系统广播action如下：</p>
<table style="width: 600px; height: 414px;">
<thead>
<tr><th style="text-align: center;">系统操作</th><th align="center">action</th></tr>
</thead>
<tbody>
<tr>
<td>监听网络变化</td>
<td align="center">android.net.conn.CONNECTIVITY_CHANGE</td>
</tr>
<tr>
<td>关闭或打开飞行模式</td>
<td align="center">Intent.ACTION_AIRPLANE_MODE_CHANGED</td>
</tr>
<tr>
<td>充电时或电量发生变化</td>
<td align="center">Intent.ACTION_BATTERY_CHANGED</td>
</tr>
<tr>
<td>电池电量低</td>
<td align="center">Intent.ACTION_BATTERY_LOW</td>
</tr>
<tr>
<td>电池电量充足（即从电量低变化到饱满时会发出广播</td>
<td align="center">Intent.ACTION_BATTERY_OKAY</td>
</tr>
<tr>
<td>系统启动完成后(仅广播一次)</td>
<td align="center">Intent.ACTION_BOOT_COMPLETED</td>
</tr>
<tr>
<td>按下照相时的拍照按键(硬件按键)时</td>
<td align="center">Intent.ACTION_CAMERA_BUTTON</td>
</tr>
<tr>
<td>屏幕锁屏</td>
<td align="center">Intent.ACTION_CLOSE_SYSTEM_DIALOGS</td>
</tr>
<tr>
<td>设备当前设置被改变时(界面语言、设备方向等)</td>
<td align="center">Intent.ACTION_CONFIGURATION_CHANGED</td>
</tr>
<tr>
<td>插入耳机时</td>
<td align="center">Intent.ACTION_HEADSET_PLUG</td>
</tr>
<tr>
<td>未正确移除SD卡但已取出来时(正确移除方法:设置–SD卡和设备内存–卸载SD卡)</td>
<td align="center">Intent.ACTION_MEDIA_BAD_REMOVAL</td>
</tr>
<tr>
<td>插入外部储存装置（如SD卡）</td>
<td align="center">Intent.ACTION_MEDIA_CHECKING</td>
</tr>
<tr>
<td>成功安装APK</td>
<td align="center">Intent.ACTION_PACKAGE_ADDED</td>
</tr>
<tr>
<td>成功删除APK</td>
<td align="center">Intent.ACTION_PACKAGE_REMOVED</td>
</tr>
<tr>
<td>重启设备</td>
<td align="center">Intent.ACTION_REBOOT</td>
</tr>
<tr>
<td>屏幕被关闭</td>
<td align="center">Intent.ACTION_SCREEN_OFF</td>
</tr>
<tr>
<td>屏幕被打开</td>
<td align="center">Intent.ACTION_SCREEN_ON</td>
</tr>
<tr>
<td>关闭系统时</td>
<td align="center">Intent.ACTION_SHUTDOWN</td>
</tr>
<tr>
<td>重启设备</td>
<td align="center">Intent.ACTION_REBOOT</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<h2>三.BroadcastReceiver 注册方式</h2>
<h3>1.静态注册</h3>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_330540" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">AndroidManifest.xml:</code></div><div class="line number2 index1 alt1">&nbsp;</div><div class="line number3 index2 alt2"><code class="html plain">&lt;</code><code class="html keyword">receiver</code> <code class="html plain">android:name=".broadcastreceiver.TestBroadcastReceiver"&gt;</code></div><div class="line number4 index3 alt1"><code class="html spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="html plain">&lt;</code><code class="html keyword">intent-filter</code><code class="html plain">&gt;</code></div><div class="line number5 index4 alt2"><code class="html spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="html plain">&lt;</code><code class="html keyword">action</code> <code class="html plain">android:name="android.net.conn.CONNECTIVITY_CHANGE" /&gt;</code></div><div class="line number6 index5 alt1"><code class="html spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="html plain">&lt;/</code><code class="html keyword">intent-filter</code><code class="html plain">&gt;</code></div><div class="line number7 index6 alt2"><code class="html plain">&lt;/</code><code class="html keyword">receiver</code><code class="html plain">&gt;</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3>2.动态注册</h3>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_390725" class="syntaxhighlighter  java"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="java plain">IntentFilter mFilter = </code><code class="java keyword">new</code> <code class="java plain">IntentFilter();</code></div><div class="line number2 index1 alt1"><code class="java plain">mFilter.addAction(ConnectivityManager.CONNECTIVITY_ACTION);</code></div><div class="line number3 index2 alt2"><code class="java plain">TestBroadcastReceiver mReceiver = </code><code class="java keyword">new</code> <code class="java plain">TestBroadcastReceiver();</code></div><div class="line number4 index3 alt1"><code class="java plain">registerReceiver(mReceiver, mFilter);</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>　　</p>
<p>&nbsp;</p>
</div>