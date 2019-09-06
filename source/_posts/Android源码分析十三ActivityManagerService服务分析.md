---
title: Android源码分析（十三）ActivityManagerService服务分析
date: 2018-07-22 13:33:13
tags: 安卓源码
categories: 
- Android
- 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/ActivityManagerService.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h3>一.ActivityManagerService(AMS) 启动过程分析</h3>
<p>在SystemServer启动ActivityManagerService</p>
<p>如果想了解SystemServer启动过程可以看这篇文章：<a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/SystemServer.html" target="_blank">Android 源码分析（六） SystemServer 进程</a></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>frameworks\<span style="color: #0000ff;">base</span><span style="color: #000000;">\services\java\com\android\server\SystemServer.java
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Activity manager runs the show.</span>
        traceBeginAndSlog(<span style="color: #800000;">"</span><span style="color: #800000;">StartActivityManager</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        mActivityManagerService </span>=<span style="color: #000000;"> mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">).getService();
        mActivityManagerService.setSystemServiceManager(mSystemServiceManager);
        mActivityManagerService.setInstaller(installer);
        </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>frameworks\base\services\core\java\com\android\server\am\ActivityManagerService.java</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> ActivityManagerService extends IActivityManager.Stub
        implements Watchdog.Monitor, BatteryStatsImpl.BatteryCallback {

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">* All system services </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    SystemServiceManager mSystemServiceManager;
    
    </span><span style="color: #008000;">/*</span><span style="color: #008000;">* Run all ActivityStacks through this </span><span style="color: #008000;">*/</span>
    <span style="color: #008000;">//</span><span style="color: #008000;">管理Activity</span>
<span style="color: #000000;">    final ActivityStackSupervisor mStackSupervisor;
    
    final ActivityStarter mActivityStarter;
    
    final TaskChangeNotificationController mTaskChangeNotificationController;

    final InstrumentationReporter mInstrumentationReporter </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> InstrumentationReporter();

    final ArrayList</span>&lt;ActiveInstrumentation&gt; mActiveInstrumentation = <span style="color: #0000ff;">new</span> ArrayList&lt;&gt;<span style="color: #000000;">();
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Whether we should use SCHED_FIFO for UI and RenderThreads.</span>
    <span style="color: #0000ff;">private</span> boolean mUseFifoUiScheduling = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
 
    </span><span style="color: #008000;">//</span><span style="color: #008000;">Broadcast 广播 ，前台广播队列和后台广播队列</span>
<span style="color: #000000;">    BroadcastQueue mFgBroadcastQueue;
    BroadcastQueue mBgBroadcastQueue;
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Convenient for easy iteration over the queues. Foreground is first
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> so that dispatch of foreground broadcasts gets precedence.</span>
    final BroadcastQueue[] mBroadcastQueues = <span style="color: #0000ff;">new</span> BroadcastQueue[<span style="color: #800080;">2</span><span style="color: #000000;">];

    BroadcastStats mLastBroadcastStats;
    BroadcastStats mCurBroadcastStats;

    BroadcastQueue broadcastQueueForIntent(Intent intent) {
        final boolean isFg </span>= (intent.getFlags() &amp; Intent.FLAG_RECEIVER_FOREGROUND) != <span style="color: #800080;">0</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_BROADCAST_BACKGROUND) Slog.i(TAG_BROADCAST,
                </span><span style="color: #800000;">"</span><span style="color: #800000;">Broadcast intent </span><span style="color: #800000;">"</span> + intent + <span style="color: #800000;">"</span><span style="color: #800000;"> on </span><span style="color: #800000;">"</span>
                + (isFg ? <span style="color: #800000;">"</span><span style="color: #800000;">foreground</span><span style="color: #800000;">"</span> : <span style="color: #800000;">"</span><span style="color: #800000;">background</span><span style="color: #800000;">"</span>) + <span style="color: #800000;">"</span><span style="color: #800000;"> queue</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span> (isFg) ?<span style="color: #000000;"> mFgBroadcastQueue : mBgBroadcastQueue;
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;">Activity 堆栈</span>
    <span style="color: #008000;">/*</span><span style="color: #008000;">*
     * The last resumed activity. This is identical to the current resumed activity most
     * of the time but could be different when we're pausing one activity before we resume
     * another activity.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> ActivityRecord mLastResumedActivity;

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * If non-null, we are tracking the time the user spends in the currently focused app.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span><span style="color: #000000;"> AppTimeTracker mCurAppTimeTracker;
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">ANR ？ 最后个ANR状态，难道可以记录app发生ANR的？</span>
    <span style="color: #008000;">/*</span><span style="color: #008000;">*
     * Dump of the activity state at the time of the last ANR. Cleared after
     * {@link WindowManagerService#LAST_ANR_LIFETIME_DURATION_MSECS}
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    String mLastANRState;
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">Service 和 Provider 管理</span>
<span style="color: #000000;">    final ActiveServices mServices;
    final ProviderMap mProviderMap;
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">存放系统数据目录
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> TODO: Move creation of battery stats service outside of activity manager service.</span>
    File dataDir =<span style="color: #000000;"> Environment.getDataDirectory();
    File systemDir </span>= <span style="color: #0000ff;">new</span> File(dataDir, <span style="color: #800000;">"</span><span style="color: #800000;">system</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    systemDir.mkdirs();
    mBatteryStatsService </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> BatteryStatsService(systemDir, mHandler);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">应用权限管理</span>
    mAppOpsService = mInjector.getAppOpsService(<span style="color: #0000ff;">new</span> File(systemDir, <span style="color: #800000;">"</span><span style="color: #800000;">appops.xml</span><span style="color: #800000;">"</span><span style="color: #000000;">), mHandler);
    
        </span><span style="color: #008000;">//</span><span style="color: #008000;">AcitivityManager 添加进来</span>
        ServiceManager.addService(Context.ACTIVITY_SERVICE, <span style="color: #0000ff;">this</span>, <span style="color: #0000ff;">true</span><span style="color: #000000;">);
                ServiceManager.addService(ProcessStats.SERVICE_NAME, mProcessStats);
                ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">meminfo</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> MemBinder(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
                ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">gfxinfo</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> GraphicsBinder(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
                ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">dbinfo</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> DbBinder(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (MONITOR_CPU_USAGE) {
                    ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">cpuinfo</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> CpuBinder(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
                }
                ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">permission</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> PermissionController(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
                ServiceManager.addService(</span><span style="color: #800000;">"</span><span style="color: #800000;">processinfo</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">new</span> ProcessInfoService(<span style="color: #0000ff;">this</span><span style="color: #000000;">));
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">最后 使用Watchdog监控</span>
    Watchdog.getInstance().addMonitor(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
    Watchdog.getInstance().addThread(mHandler);
    
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;&nbsp; 从上面截取的一些代码片段，我们能了解到， AMS创建过程 涉及到Android 四大组件管理的初始化工作。并且ActivityManagerService extends IActivityManager.Stub，所以可知AcitivityManagerService与AcitivityManager之间通信也是使用binder机制。</p>
<p>&nbsp; &nbsp; 进ActivityManager 里面看看</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">与ActivityManagerService里的ServiceManager.addService(Context.ACTIVITY_SERVICE, this, true);对应。</span>
<span style="color: #000000;">@SystemService(Context.ACTIVITY_SERVICE)
</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> ActivityManager {
    ...
}</span></pre>
</div>
<p>&nbsp;</p>
<h3>二.ActivityManager和ActivityManagerService关系</h3>
<p>如果想了解Activity是如果通过ActivityManager调用ActivityManagerService的过程可以看下这篇文章.</p>
<p><a id="homepage1_HomePageDays_DaysList_ctl03_DayList_TitleUrl_1" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/startActivity.html" target="_blank">Android 源码分析（二） Activity 启动分析</a></p>
<p>ActivityManager（frameworks/base/core/java/android/app/ActivityManager.java）</p>
<p>ActivityManager 是客户端用来管理系统中正在运行的所有Activity.</p>
<p>上层APP通过ActivityManager使用binder机制传递信息给AMS，由AMS去完成交互和调度工作后通过binder机制返回给ActivityManager，把结果在返回给上层app。</p>
<p>一张图了解ActivityManager和ActivityManagerService在整个Android系统通信过程中位置。</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180722132754102-1009841250.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>参考：<br>[1]https://www.cnblogs.com/bastard/p/5770573.html</p>
</div>