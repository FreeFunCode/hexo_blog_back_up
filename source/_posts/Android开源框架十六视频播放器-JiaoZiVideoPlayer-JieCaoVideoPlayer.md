---
title: Android 开源框架 ( 十六 ) 视频播放器 --- JiaoZiVideoPlayer(JieCaoVideoPlayer)
date: 2018-08-12 16:07:37
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/JiaoZiVideoPlayer.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　原JieCaoVideoPlayer 不知道什么时候改名了，现在叫JiaoZiVideoPlayer.</p>
<p>　　项目GitHub地址：<a href="https://github.com/lipangit/JiaoZiVideoPlayer" target="_blank">https://github.com/lipangit/JiaoZiVideoPlayer</a></p>
<p>　　<strong>JiaoZiVideoPlayer主要功能</strong>：</p>
<div class="cnblogs_Highlighter">
<pre class="brush:csharp;gutter:true;">    1.视频全屏播放和浮层小窗播放
    2.可以完全自定义UI
    3.能在ListView、ViewPager和ListView、ViewPager和Fragment等多重嵌套模式下全屏工作
    4.手势修改进度和音量
    5.视频大小的屏幕适配，宽或长至少有两个对边是充满屏幕的，另外两个方向居中
    6.可以在加载、暂停、播放等各种状态中正常进入全屏和退出全屏
    7.基于exoplayer, 支持hls,rtsp
    8.设置http头信息
    9.重力感应自动全屏
    10.WebView嵌套本地视频
</pre>
</div>
<p>　　</p>
<h2>　基本使用</h2>
<h3>　　1.添加依赖</h3>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">cn.jzvd:jiaozivideoplayer:6.2.12</span><span style="color: #800000;">'</span></pre>
</div>
<h3>　　2.xml布局文件</h3>
<div class="cnblogs_code">
<pre>&lt;<span style="color: #000000;">cn.jzvd.JZVideoPlayerStandard
        android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/videoplayer</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">200dp</span><span style="color: #800000;">"</span>/&gt;</pre>
</div>
<h3>　　3.设置视频url和标题</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>JZVideoPlayerStandard jzVideoPlayerStandard =<span style="color: #000000;"> (JZVideoPlayerStandard) findViewById(R.id.videoplayer);
jzVideoPlayerStandard.setUp(</span><span style="color: #800000;">"</span><span style="color: #800000;">http://jzvd.nathen.cn/c6e3dc12a1154626b3476d9bf3bd7266/6b56c5f0dc31428083757a45764763b0-5287d2089db37e62345123a1be272f8b.mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">, 
                            JZVideoPlayerStandard.SCREEN_WINDOW_NORMAL, 
                            </span><span style="color: #800000;">"</span><span style="color: #800000;">饺子闭眼睛</span><span style="color: #800000;">"</span><span style="color: #000000;">);
jzVideoPlayerStandard.thumbImageView.setImage(</span><span style="color: #800000;">"</span><span style="color: #800000;">http://p.qpic.cn/videoyun/0/2449_43b6f696980311e59ed467f22794e792_1/640</span><span style="color: #800000;">"</span>);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　4.AndroidManifest.xml：</h3>
<div class="cnblogs_code">
<pre>&lt;<span style="color: #000000;">activity
    android:name</span>=<span style="color: #800000;">"</span><span style="color: #800000;">.MainActivity</span><span style="color: #800000;">"</span><span style="color: #000000;">
    android:configChanges</span>=<span style="color: #800000;">"</span><span style="color: #800000;">orientation|screenSize|keyboardHidden</span><span style="color: #800000;">"</span><span style="color: #000000;">
    android:screenOrientation</span>=<span style="color: #800000;">"</span><span style="color: #800000;">portrait</span><span style="color: #800000;">"</span> /&gt;
    </pre>
</div>
<p>&nbsp;　　当我们横竖屏切换的时候会直接调用onCreate方法中的onConfigurationChanged方法，而不会重新执行onCreate方法，那当然如果不配置这个属性的话就会重新调用onCreate方法了。</p>
<h2>&nbsp;</h2>
</div>