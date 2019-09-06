---
title: Android 源码分析（三） Service 启动分析
date: 2018-06-24 14:24:40
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/startService.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p><img src="https://images2018.cnblogs.com/blog/612293/201806/612293-20180624141726420-1470284606.png" alt=""></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\core\java\android\content\Context.java</span>

<span style="color: #008000;">/**</span><span style="color: #008000;">
* startService是Context的抽象方法。
* 调用startService时，会先调用到ContextWrapper的startService方法
</span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Nullable
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">abstract</span> ComponentName startService(Intent service);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\core\java\android\content\ContextWrapper.java</span>

<span style="color: #008000;">/**</span><span style="color: #008000;">
* mBase是ContextImpl的实例，
* ContextWrapper类的startService方法最终通过ContextImpl类的startService方法来实现
</span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> ComponentName startService(Intent service) {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mBase.startService(service);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\core\java\android\app\ContextImpl.java</span>
<span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> ComponentName startService(Intent service) {
        warnIfCallingFromSystemProcess();
        </span><span style="color: #0000ff;">return</span> startServiceCommon(service, <span style="color: #0000ff;">false</span><span style="color: #000000;">, mUser);
    }

    </span><span style="color: #0000ff;">private</span> ComponentName startServiceCommon(Intent service, <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> requireForeground,
            UserHandle user) {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;验证intent的有效性&nbsp;&nbsp;</span>
<span style="color: #000000;">            validateServiceIntent(service);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;准备离开应用程序进程，进入ActivityManagerService进程（意味着bundle的数据要在进程间传递）</span>
            service.prepareToLeaveProcess(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;调用ActivityManagerProxy类的startService来实现启动服务的操作&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;ActivityManagerProxy是一个Binder对象的远程接口，而这个Binder对象就是ActivityManagerService。</span>
            ComponentName cn =<span style="color: #000000;"> ActivityManager.getService().startService(
                mMainThread.getApplicationThread(), service, service.resolveTypeIfNeeded(
                            getContentResolver()), requireForeground,
                            getOpPackageName(), user.getIdentifier());
            </span><span style="color: #0000ff;">if</span> (cn != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">if</span> (cn.getPackageName().equals("!"<span style="color: #000000;">)) {
                    </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> SecurityException(
                            </span>"Not allowed to start service " +<span style="color: #000000;"> service
                            </span>+ " without permission " +<span style="color: #000000;"> cn.getClassName());
                } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (cn.getPackageName().equals("!!"<span style="color: #000000;">)) {
                    </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> SecurityException(
                            </span>"Unable to start service " +<span style="color: #000000;"> service
                            </span>+ ": " +<span style="color: #000000;"> cn.getClassName());
                } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (cn.getPackageName().equals("?"<span style="color: #000000;">)) {
                    </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> IllegalStateException(
                            </span>"Not allowed to start service " + service + ": " +<span style="color: #000000;"> cn.getClassName());
                }
            }
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> cn;
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (RemoteException e) {
            </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> e.rethrowFromSystemServer();
        }
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">

    * 验证intent的有效性&nbsp;
    * 
    </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> validateServiceIntent(Intent service) {
        </span><span style="color: #0000ff;">if</span> (service.getComponent() == <span style="color: #0000ff;">null</span> &amp;&amp; service.getPackage() == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;隐式启动判断，5.1之后不允许隐式启动服务&nbsp;&nbsp;</span>
            <span style="color: #0000ff;">if</span> (getApplicationInfo().targetSdkVersion &gt;=<span style="color: #000000;"> Build.VERSION_CODES.LOLLIPOP) {
                IllegalArgumentException ex </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> IllegalArgumentException(
                        </span>"Service Intent must be explicit: " +<span style="color: #000000;"> service);
                </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> ex;
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                Log.w(TAG, </span>"Implicit intents with startService are not safe: " +<span style="color: #000000;"> service
                        </span>+ " " + Debug.getCallers(2, 3<span style="color: #000000;">));
            }
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\core\java\android\app\ActivityManager.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span><span style="color: #000000;"> IActivityManager getService() {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> IActivityManagerSingleton.get();
    }
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">final</span> Singleton&lt;IActivityManager&gt; IActivityManagerSingleton =
            <span style="color: #0000ff;">new</span> Singleton&lt;IActivityManager&gt;<span style="color: #000000;">() {
                @Override
                </span><span style="color: #0000ff;">protected</span><span style="color: #000000;"> IActivityManager create() {
                    </span><span style="color: #0000ff;">final</span> IBinder b =<span style="color: #000000;"> ServiceManager.getService(Context.ACTIVITY_SERVICE);
                    </span><span style="color: #0000ff;">final</span> IActivityManager am =<span style="color: #000000;"> IActivityManager.Stub.asInterface(b);
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> am;
                }
            };</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\services\core\java\com\android\server\am\ActivityManagerService.java</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> ActivityManagerService <span style="color: #0000ff;">extends</span><span style="color: #000000;"> IActivityManager.Stub
        </span><span style="color: #0000ff;">implements</span> Watchdog.Monitor, BatteryStatsImpl.BatteryCallback {}</pre>
</div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\services\core\java\com\android\server\am\ActivityManagerService.java</span>
<span style="color: #000000;">    @Override
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> ComponentName startService(IApplicationThread caller, Intent service,
            String resolvedType, </span><span style="color: #0000ff;">boolean</span> requireForeground, String callingPackage, <span style="color: #0000ff;">int</span><span style="color: #000000;"> userId)
            </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> TransactionTooLargeException {
        enforceNotIsolatedCaller(</span>"startService"<span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Refuse possible leaked file descriptors</span>
        <span style="color: #0000ff;">if</span> (service != <span style="color: #0000ff;">null</span> &amp;&amp; service.hasFileDescriptors() == <span style="color: #0000ff;">true</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalArgumentException("File descriptors passed in Intent"<span style="color: #000000;">);
        }

        </span><span style="color: #0000ff;">if</span> (callingPackage == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalArgumentException("callingPackage cannot be null"<span style="color: #000000;">);
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_SERVICE) Slog.v(TAG_SERVICE,
                </span>"*** startService: " + service + " type=" + resolvedType + " fg=" +<span style="color: #000000;"> requireForeground);
        </span><span style="color: #0000ff;">synchronized</span>(<span style="color: #0000ff;">this</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> callingPid =<span style="color: #000000;"> Binder.getCallingPid();
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> callingUid =<span style="color: #000000;"> Binder.getCallingUid();
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> origId =<span style="color: #000000;"> Binder.clearCallingIdentity();
            ComponentName res;
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                res </span>=<span style="color: #000000;"> mServices.startServiceLocked(caller, service,
                        resolvedType, callingPid, callingUid,
                        requireForeground, callingPackage, userId);
            } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
                Binder.restoreCallingIdentity(origId);
            }
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> res;
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\services\core\java\com\android\server\am\ActiveServices.java</span>
<span style="color: #000000;">
ComponentName startServiceLocked(IApplicationThread caller, Intent service, String resolvedType,
            </span><span style="color: #0000ff;">int</span> callingPid, <span style="color: #0000ff;">int</span> callingUid, <span style="color: #0000ff;">boolean</span> fgRequired, String callingPackage, <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span><span style="color: #000000;"> userId)
            </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> TransactionTooLargeException {
        </span><span style="color: #0000ff;">if</span> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE, "startService: " +<span style="color: #000000;"> service
                </span>+ " type=" + resolvedType + " args=" +<span style="color: #000000;"> service.getExtras());

        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> callerFg;
        </span><span style="color: #0000ff;">if</span> (caller != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">final</span> ProcessRecord callerApp =<span style="color: #000000;"> mAm.getRecordForAppLocked(caller);
            </span><span style="color: #0000ff;">if</span> (callerApp == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> SecurityException(
                        </span>"Unable to find app for caller " +<span style="color: #000000;"> caller
                        </span>+ " (pid=" +<span style="color: #000000;"> callingPid
                        </span>+ ") when starting service " +<span style="color: #000000;"> service);
            }
            callerFg </span>= callerApp.setSchedGroup !=<span style="color: #000000;"> ProcessList.SCHED_GROUP_BACKGROUND;
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            callerFg </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }

        
    &nbsp;</span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;调用retrieveServiceLocked方法解析service这个Intent，然后将解析结果放在res.record中。&nbsp;&nbsp;
    &nbsp;</span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;调用该方法后，为被调用者构造了对应的ServiceRecord对象，并保存到ActivityManagerService的成员变量mServiceMap中。&nbsp;</span>
        ServiceLookupResult res =<span style="color: #000000;">
            retrieveServiceLocked(service, resolvedType, callingPackage,
                    callingPid, callingUid, userId, </span><span style="color: #0000ff;">true</span>, callerFg, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">if</span> (res == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">if</span> (res.record == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span> ComponentName("!", res.permission != <span style="color: #0000ff;">null</span>
                    ? res.permission : "private to package"<span style="color: #000000;">);
        }

        ServiceRecord r </span>=<span style="color: #000000;"> res.record;

        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mAm.mUserController.exists(r.userId)) {
            Slog.w(TAG, </span>"Trying to start service with non-existent user! " +<span style="color: #000000;"> r.userId);
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If this isn't a direct-to-foreground start, check our ability to kick off an
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> arbitrary service</span>
        <span style="color: #0000ff;">if</span> (!r.startRequested &amp;&amp; !<span style="color: #000000;">fgRequired) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Before going further -- if this app is not allowed to start services in the
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> background, then at this point we aren't going to let it period.</span>
            <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> allowed =<span style="color: #000000;"> mAm.getAppStartModeLocked(r.appInfo.uid, r.packageName,
                    r.appInfo.targetSdkVersion, callingPid, </span><span style="color: #0000ff;">false</span>, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (allowed !=<span style="color: #000000;"> ActivityManager.APP_START_MODE_NORMAL) {
                Slog.w(TAG, </span>"Background start not allowed: service "
                        + service + " to " +<span style="color: #000000;"> r.name.flattenToShortString()
                        </span>+ " from pid=" + callingPid + " uid=" +<span style="color: #000000;"> callingUid
                        </span>+ " pkg=" +<span style="color: #000000;"> callingPackage);
                </span><span style="color: #0000ff;">if</span> (allowed ==<span style="color: #000000;"> ActivityManager.APP_START_MODE_DELAYED) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> In this case we are silently disabling the app, to disrupt as
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> little as possible existing apps.</span>
                    <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                }
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> This app knows it is in the new model where this operation is not
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> allowed, so tell it what has happened.</span>
                UidRecord uidRec =<span style="color: #000000;"> mAm.mActiveUids.get(r.appInfo.uid);
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span> ComponentName("?", "app is in background uid " +<span style="color: #000000;"> uidRec);
            }
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;权限检查&nbsp;</span>
        NeededUriGrants neededGrants =<span style="color: #000000;"> mAm.checkGrantUriPermissionFromIntentLocked(
                callingUid, r.packageName, service, service.getFlags(), </span><span style="color: #0000ff;">null</span><span style="color: #000000;">, r.userId);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If permissions need a review before any of the app components can run,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> we do not start the service and launch a review activity if the calling app
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> is in the foreground passing it a pending intent to start the service when
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> review is completed.</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (mAm.mPermissionReviewRequired) {
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">requestStartTargetPermissionsReviewIfNeededLocked(r, callingPackage,
                    callingUid, service, callerFg, userId)) {
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
            }
        }

        </span><span style="color: #0000ff;">if</span> (unscheduleServiceRestartLocked(r, callingUid, <span style="color: #0000ff;">false</span><span style="color: #000000;">)) {
            </span><span style="color: #0000ff;">if</span> (DEBUG_SERVICE) Slog.v(TAG_SERVICE, "START SERVICE WHILE RESTART PENDING: " +<span style="color: #000000;"> r);
        }
        r.lastActivity </span>=<span style="color: #000000;"> SystemClock.uptimeMillis();
        r.startRequested </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        r.delayedStop </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        r.fgRequired </span>=<span style="color: #000000;"> fgRequired;
        r.pendingStarts.add(</span><span style="color: #0000ff;">new</span> ServiceRecord.StartItem(r, <span style="color: #0000ff;">false</span><span style="color: #000000;">, r.makeNextStartId(),
                service, neededGrants, callingUid));

        </span><span style="color: #0000ff;">final</span> ServiceMap smap =<span style="color: #000000;"> getServiceMapLocked(r.userId);
        </span><span style="color: #0000ff;">boolean</span> addToStarting = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span> (!callerFg &amp;&amp; !fgRequired &amp;&amp; r.app == <span style="color: #0000ff;">null</span>
                &amp;&amp;<span style="color: #000000;"> mAm.mUserController.hasStartedUserState(r.userId)) {
            ProcessRecord proc </span>= mAm.getProcessRecordLocked(r.processName, r.appInfo.uid, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (proc == <span style="color: #0000ff;">null</span> || proc.curProcState &gt;<span style="color: #000000;"> ActivityManager.PROCESS_STATE_RECEIVER) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> If this is not coming from a foreground caller, then we may want
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> to delay the start if there are already other background services
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> that are starting.  This is to avoid process start spam when lots
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> of applications are all handling things like connectivity broadcasts.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> We only do this for cached processes, because otherwise an application
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> can have assumptions about calling startService() for a service to run
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> in its own process, and for that process to not be killed before the
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> service is started.  This is especially the case for receivers, which
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> may start a service in onReceive() to do some additional work and have
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> initialized some global state as part of that.</span>
                <span style="color: #0000ff;">if</span> (DEBUG_DELAYED_SERVICE) Slog.v(TAG_SERVICE, "Potential start delay of "
                        + r + " in " +<span style="color: #000000;"> proc);
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.delayed) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> This service is already scheduled for a delayed start; just leave
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> it still waiting.</span>
                    <span style="color: #0000ff;">if</span> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE, "Continuing to delay: " +<span style="color: #000000;"> r);
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> r.name;
                }
                </span><span style="color: #0000ff;">if</span> (smap.mStartingBackground.size() &gt;=<span style="color: #000000;"> mMaxStartingBackground) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Something else is starting, delay!</span>
                    Slog.i(TAG_SERVICE, "Delaying start of: " +<span style="color: #000000;"> r);
                    smap.mDelayedStartList.add(r);
                    r.delayed </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> r.name;
                }
                </span><span style="color: #0000ff;">if</span> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE, "Not delaying: " +<span style="color: #000000;"> r);
                addToStarting </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (proc.curProcState &gt;=<span style="color: #000000;"> ActivityManager.PROCESS_STATE_SERVICE) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> We slightly loosen when we will enqueue this new service as a background
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> starting service we are waiting for, to also include processes that are
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> currently running other services or receivers.</span>
                addToStarting = <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE,
                        </span>"Not delaying, but counting as bg: " +<span style="color: #000000;"> r);
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) {
                StringBuilder sb </span>= <span style="color: #0000ff;">new</span> StringBuilder(128<span style="color: #000000;">);
                sb.append(</span>"Not potential delay (state="<span style="color: #000000;">).append(proc.curProcState)
                        .append(</span>' '<span style="color: #000000;">).append(proc.adjType);
                String reason </span>=<span style="color: #000000;"> proc.makeAdjReason();
                </span><span style="color: #0000ff;">if</span> (reason != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    sb.append(</span>' '<span style="color: #000000;">);
                    sb.append(reason);
                }
                sb.append(</span>"): "<span style="color: #000000;">);
                sb.append(r.toString());
                Slog.v(TAG_SERVICE, sb.toString());
            }
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) {
            </span><span style="color: #0000ff;">if</span> (callerFg ||<span style="color: #000000;"> fgRequired) {
                Slog.v(TAG_SERVICE, </span>"Not potential delay (callerFg=" + callerFg + " uid="
                        + callingUid + " pid=" + callingPid + " fgRequired=" + fgRequired + "): " +<span style="color: #000000;"> r);
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (r.app != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                Slog.v(TAG_SERVICE, </span>"Not potential delay (cur app=" + r.app + "): " +<span style="color: #000000;"> r);
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                Slog.v(TAG_SERVICE,
                        </span>"Not potential delay (user " + r.userId + " not started): " +<span style="color: #000000;"> r);
            }
        }

        ComponentName cmp </span>=<span style="color: #000000;"> startServiceInnerLocked(smap, service, r, callerFg, addToStarting);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> cmp;
    }

    ComponentName startServiceInnerLocked(ServiceMap smap, Intent service, ServiceRecord r,
            </span><span style="color: #0000ff;">boolean</span> callerFg, <span style="color: #0000ff;">boolean</span> addToStarting) <span style="color: #0000ff;">throws</span><span style="color: #000000;"> TransactionTooLargeException {
        ServiceState stracker </span>=<span style="color: #000000;"> r.getTracker();
        </span><span style="color: #0000ff;">if</span> (stracker != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            stracker.setStarted(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">, mAm.mProcessStats.getMemFactorLocked(), r.lastActivity);
        }
        r.callStart </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">synchronized</span><span style="color: #000000;"> (r.stats.getBatteryStats()) {
            r.stats.startRunningLocked();
        }
        String error </span>= bringUpServiceLocked(r, service.getFlags(), callerFg, <span style="color: #0000ff;">false</span>, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">if</span> (error != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span> ComponentName("!!"<span style="color: #000000;">, error);
        }

        </span><span style="color: #0000ff;">if</span> (r.startRequested &amp;&amp;<span style="color: #000000;"> addToStarting) {
            </span><span style="color: #0000ff;">boolean</span> first = smap.mStartingBackground.size() == 0<span style="color: #000000;">;
            smap.mStartingBackground.add(r);
            r.startingBgTimeout </span>= SystemClock.uptimeMillis() +<span style="color: #000000;"> mAm.mConstants.BG_START_TIMEOUT;
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_SERVICE) {
                RuntimeException here </span>= <span style="color: #0000ff;">new</span> RuntimeException("here"<span style="color: #000000;">);
                here.fillInStackTrace();
                Slog.v(TAG_SERVICE, </span>"Starting background (first=" + first + "): " +<span style="color: #000000;"> r, here);
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) {
                Slog.v(TAG_SERVICE, </span>"Starting background (first=" + first + "): " +<span style="color: #000000;"> r);
            }
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (first) {
                smap.rescheduleDelayedStartsLocked();
            }
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (callerFg ||<span style="color: #000000;"> r.fgRequired) {
            smap.ensureNotStartingBackgroundLocked(r);
        }

        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> r.name;
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
    *1.创建进程：startProcessLocked
    *2.第一次启动Service：realStartServiceLocked
    *3.启动已有的Service：sendServiceArgsLocked
    </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> String bringUpServiceLocked(ServiceRecord r, <span style="color: #0000ff;">int</span> intentFlags, <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> execInFg,
            </span><span style="color: #0000ff;">boolean</span> whileRestarting, <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> permissionsReviewRequired)
            </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> TransactionTooLargeException {
        </span><span style="color: #008000;">//</span><span style="color: #008000;">Slog.i(TAG, "Bring up service:");
        </span><span style="color: #008000;">//</span><span style="color: #008000;">r.dump("  ");</span>

        <span style="color: #0000ff;">if</span> (r.app != <span style="color: #0000ff;">null</span> &amp;&amp; r.app.thread != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;启动Service&nbsp;&nbsp;</span>
            sendServiceArgsLocked(r, execInFg, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">if</span> (!whileRestarting &amp;&amp;<span style="color: #000000;"> mRestartingServices.contains(r)) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If waiting for a restart, then do nothing.</span>
            <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_SERVICE) {
            Slog.v(TAG_SERVICE, </span>"Bringing up " + r + " " + r.intent + " fg=" +<span style="color: #000000;"> r.fgRequired);
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> We are now bringing the service up, so no longer in the
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> restarting state.</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (mRestartingServices.remove(r)) {
            clearRestartingIfNeededLocked(r);
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make sure this service is no longer considered delayed, we are starting it now.</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.delayed) {
            </span><span style="color: #0000ff;">if</span> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE, "REM FR DELAY LIST (bring up): " +<span style="color: #000000;"> r);
            getServiceMapLocked(r.userId).mDelayedStartList.remove(r);
            r.delayed </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make sure that the user who owns this service is started.  If not,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> we don't want to allow it to run.</span>
        <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mAm.mUserController.hasStartedUserState(r.userId)) {
            String msg </span>= "Unable to launch app "
                    + r.appInfo.packageName + "/"
                    + r.appInfo.uid + " for service "
                    + r.intent.getIntent() + ": user " + r.userId + " is stopped"<span style="color: #000000;">;
            Slog.w(TAG, msg);
            bringDownServiceLocked(r);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> msg;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Service is now being launched, its package can't be stopped.</span>
        <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            AppGlobals.getPackageManager().setPackageStoppedState(
                    r.packageName, </span><span style="color: #0000ff;">false</span><span style="color: #000000;">, r.userId);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (RemoteException e) {
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IllegalArgumentException e) {
            Slog.w(TAG, </span>"Failed trying to unstop package "
                    + r.packageName + ": " +<span style="color: #000000;"> e);
        }

        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> isolated = (r.serviceInfo.flags&amp;ServiceInfo.FLAG_ISOLATED_PROCESS) != 0<span style="color: #000000;">;
        </span><span style="color: #0000ff;">final</span> String procName =<span style="color: #000000;"> r.processName;
        String hostingType </span>= "service"<span style="color: #000000;">;
        ProcessRecord app;

        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">isolated) {
            app </span>= mAm.getProcessRecordLocked(procName, r.appInfo.uid, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (DEBUG_MU) Slog.v(TAG_MU, "bringUpServiceLocked: appInfo.uid=" +<span style="color: #000000;"> r.appInfo.uid
                        </span>+ " app=" +<span style="color: #000000;"> app);
            </span><span style="color: #0000ff;">if</span> (app != <span style="color: #0000ff;">null</span> &amp;&amp; app.thread != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                    app.addPackage(r.appInfo.packageName, r.appInfo.versionCode, mAm.mProcessStats);
                    realStartServiceLocked(r, app, execInFg);
                    </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (TransactionTooLargeException e) {
                    </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> e;
                } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (RemoteException e) {
                    Slog.w(TAG, </span>"Exception when starting service " +<span style="color: #000000;"> r.shortName, e);
                }

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> If a dead object exception was thrown -- fall through to
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> restart the application.</span>
<span style="color: #000000;">            }
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If this service runs in an isolated process, then each time
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> we call startProcessLocked() we will get a new isolated
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> process, starting another process if we are currently waiting
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> for a previous process to come up.  To deal with this, we store
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> in the service any current isolated process it is running in or
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> waiting to have come up.</span>
            app =<span style="color: #000000;"> r.isolatedProc;
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (WebViewZygote.isMultiprocessEnabled()
                    </span>&amp;&amp;<span style="color: #000000;"> r.serviceInfo.packageName.equals(WebViewZygote.getPackageName())) {
                hostingType </span>= "webview_service"<span style="color: #000000;">;
            }
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Not running -- get it started, and enqueue this service record
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> to be executed when the app comes up.</span>
        <span style="color: #0000ff;">if</span> (app == <span style="color: #0000ff;">null</span> &amp;&amp; !<span style="color: #000000;">permissionsReviewRequired) {
            </span><span style="color: #0000ff;">if</span> ((app=mAm.startProcessLocked(procName, r.appInfo, <span style="color: #0000ff;">true</span><span style="color: #000000;">, intentFlags,
                    hostingType, r.name, </span><span style="color: #0000ff;">false</span>, isolated, <span style="color: #0000ff;">false</span>)) == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                String msg </span>= "Unable to launch app "
                        + r.appInfo.packageName + "/"
                        + r.appInfo.uid + " for service "
                        + r.intent.getIntent() + ": process is bad"<span style="color: #000000;">;
                Slog.w(TAG, msg);
                bringDownServiceLocked(r);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> msg;
            }
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (isolated) {
                r.isolatedProc </span>=<span style="color: #000000;"> app;
            }
        }

        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mPendingServices.contains(r)) {
            mPendingServices.add(r);
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.delayedStop) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Oh and hey we've already been asked to stop!</span>
            r.delayedStop = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.startRequested) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE,
                        </span>"Applying delayed stop (in bring up): " +<span style="color: #000000;"> r);
                stopServiceLocked(r);
            }
        }

        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
    * 完成了Service的创建、绑定、启动的调用
    </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> realStartServiceLocked(ServiceRecord r,
            ProcessRecord app, </span><span style="color: #0000ff;">boolean</span> execInFg) <span style="color: #0000ff;">throws</span><span style="color: #000000;"> RemoteException {
        </span><span style="color: #0000ff;">if</span> (app.thread == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RemoteException();
        }
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_MU)
            Slog.v(TAG_MU, </span>"realStartServiceLocked, ServiceRecord.uid = " +<span style="color: #000000;"> r.appInfo.uid
                    </span>+ ", ProcessRecord.uid = " +<span style="color: #000000;"> app.uid);
        r.app </span>=<span style="color: #000000;"> app;
        r.restartTime </span>= r.lastActivity =<span style="color: #000000;"> SystemClock.uptimeMillis();

        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> newService =<span style="color: #000000;"> app.services.add(r);
        bumpServiceExecutingLocked(r, execInFg, </span>"create"<span style="color: #000000;">);
        mAm.updateLruProcessLocked(app, </span><span style="color: #0000ff;">false</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
        updateServiceForegroundLocked(r.app, </span><span style="color: #008000;">/*</span><span style="color: #008000;"> oomAdj= </span><span style="color: #008000;">*/</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">);
        mAm.updateOomAdjLocked();

        </span><span style="color: #0000ff;">boolean</span> created = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (LOG_SERVICE_START_STOP) {
                String nameTerm;
                </span><span style="color: #0000ff;">int</span> lastPeriod = r.shortName.lastIndexOf('.'<span style="color: #000000;">);
                nameTerm </span>= lastPeriod &gt;= 0 ?<span style="color: #000000;"> r.shortName.substring(lastPeriod) : r.shortName;
                EventLogTags.writeAmCreateService(
                        r.userId, System.identityHashCode(r), nameTerm, r.app.uid, r.app.pid);
            }
            </span><span style="color: #0000ff;">synchronized</span><span style="color: #000000;"> (r.stats.getBatteryStats()) {
                r.stats.startLaunchedLocked();
            }
            mAm.notifyPackageUse(r.serviceInfo.packageName,
                                 PackageManager.NOTIFY_PACKAGE_USE_SERVICE);
            app.forceProcessStateUpTo(ActivityManager.PROCESS_STATE_SERVICE);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;RPC调用ActivityThread类中的方法创建Service&nbsp;</span>
<span style="color: #000000;">            app.thread.scheduleCreateService(r, r.serviceInfo,
                    mAm.compatibilityInfoForPackageLocked(r.serviceInfo.applicationInfo),
                    app.repProcState);
            r.postNotification();
            created </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (DeadObjectException e) {
            Slog.w(TAG, </span>"Application dead when creating service " +<span style="color: #000000;"> r);
            mAm.appDiedLocked(app);
            </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> e;
        } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">created) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Keep the executeNesting count accurate.</span>
                <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> inDestroying =<span style="color: #000000;"> mDestroyingServices.contains(r);
                serviceDoneExecutingLocked(r, inDestroying, inDestroying);

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Cleanup.</span>
                <span style="color: #0000ff;">if</span><span style="color: #000000;"> (newService) {
                    app.services.remove(r);
                    r.app </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                }

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Retry.</span>
                <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">inDestroying) {
                    scheduleServiceRestartLocked(r, </span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
                }
            }
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.whitelistManager) {
            app.whitelistManager </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }

        &nbsp;</span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;绑定该Service&nbsp;&nbsp;</span>
<span style="color: #000000;">        requestServiceBindingsLocked(r, execInFg);

        updateServiceClientActivitiesLocked(app, </span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">true</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the service is in the started state, and there are no
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> pending arguments, then fake up one so its onStartCommand() will
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> be called.</span>
        <span style="color: #0000ff;">if</span> (r.startRequested &amp;&amp; r.callStart &amp;&amp; r.pendingStarts.size() == 0<span style="color: #000000;">) {
            r.pendingStarts.add(</span><span style="color: #0000ff;">new</span> ServiceRecord.StartItem(r, <span style="color: #0000ff;">false</span><span style="color: #000000;">, r.makeNextStartId(),
                    </span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span>, 0<span style="color: #000000;">));
        }

        &nbsp;</span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;启动该Service&nbsp;&nbsp;</span>
        sendServiceArgsLocked(r, execInFg, <span style="color: #0000ff;">true</span><span style="color: #000000;">);

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.delayed) {
            </span><span style="color: #0000ff;">if</span> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE, "REM FR DELAY LIST (new proc): " +<span style="color: #000000;"> r);
            getServiceMapLocked(r.userId).mDelayedStartList.remove(r);
            r.delayed </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.delayedStop) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Oh and hey we've already been asked to stop!</span>
            r.delayedStop = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (r.startRequested) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG_DELAYED_STARTS) Slog.v(TAG_SERVICE,
                        </span>"Applying delayed stop (from start): " +<span style="color: #000000;"> r);
                stopServiceLocked(r);
            }
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">android-8.0.0_r1\frameworks\base\core\java\android\app\ActivityThread.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> scheduleCreateService(IBinder token,
                ServiceInfo info, CompatibilityInfo compatInfo, </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> processState) {
            updateProcessState(processState, </span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
            CreateServiceData s </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> CreateServiceData();
            s.token </span>=<span style="color: #000000;"> token;
            s.info </span>=<span style="color: #000000;"> info;
            s.compatInfo </span>=<span style="color: #000000;"> compatInfo;

    </span><span style="color: #008000;">//</span><span style="color: #008000;">通过sendMessage方法向应用程序主线程MessageQueue中发送一个CREATE_SERVICE消息</span>
<span style="color: #000000;">            sendMessage(H.CREATE_SERVICE, s);
        }

 </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> handleMessage(Message msg) {
            </span><span style="color: #0000ff;">if</span> (DEBUG_MESSAGES) Slog.v(TAG, "&gt;&gt;&gt; handling: " +<span style="color: #000000;"> codeToString(msg.what));
            </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (msg.what) {                
                ........

            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> CREATE_SERVICE:
                    Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, (</span>"serviceCreate: " +<span style="color: #000000;"> String.valueOf(msg.obj)));
</span><span style="color: #008000;">//</span><span style="color: #008000;">    将Service的创建工作交给ActivityThread类的handleCreateService方法来完成</span>
<span style="color: #000000;">                    handleCreateService((CreateServiceData)msg.obj);
                    Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;

                    ........
                }
     }

 
</span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> handleCreateService(CreateServiceData data) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If we are getting ready to gc after going to the background, well
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> we are back active so skip it.</span>
<span style="color: #000000;">        unscheduleGcIdler();

        LoadedApk packageInfo </span>=<span style="color: #000000;"> getPackageInfoNoCheck(
                data.info.applicationInfo, data.compatInfo);
        Service service </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            java.lang.ClassLoader cl </span>=<span style="color: #000000;"> packageInfo.getClassLoader();
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;data.info.name就是Service是类名，通过类加载器根据类名创建一个实例后强转为Service类的实例&nbsp;&nbsp;</span>
            service =<span style="color: #000000;"> (Service) cl.loadClass(data.info.name).newInstance();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mInstrumentation.onException(service, e)) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(
                    </span>"Unable to instantiate service " +<span style="color: #000000;"> data.info.name
                    </span>+ ": " +<span style="color: #000000;"> e.toString(), e);
            }
        }

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span> (localLOGV) Slog.v(TAG, "Creating service " +<span style="color: #000000;"> data.info.name);

            ContextImpl context </span>= ContextImpl.createAppContext(<span style="color: #0000ff;">this</span><span style="color: #000000;">, packageInfo);
            context.setOuterContext(service);

            Application app </span>= packageInfo.makeApplication(<span style="color: #0000ff;">false</span><span style="color: #000000;">, mInstrumentation);
            service.attach(context, </span><span style="color: #0000ff;">this</span><span style="color: #000000;">, data.info.name, data.token, app,
                    ActivityManager.getService());
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;回调Service的onCreate()方法&nbsp;&nbsp;</span>
<span style="color: #000000;">            service.onCreate();
            </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;mServices保存了应用程序进程中所有的Service，把Service添加到该变量中&nbsp;&nbsp;</span>
<span style="color: #000000;">            mServices.put(data.token, service);
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">&nbsp;通知ActivityManagerService，当前Service创建完成&nbsp;&nbsp;</span>
<span style="color: #000000;">                ActivityManager.getService().serviceDoneExecuting(
                        data.token, SERVICE_DONE_EXECUTING_ANON, </span>0, 0<span style="color: #000000;">);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (RemoteException e) {
                </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> e.rethrowFromSystemServer();
            }
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mInstrumentation.onException(service, e)) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(
                    </span>"Unable to create service " +<span style="color: #000000;"> data.info.name
                    </span>+ ": " +<span style="color: #000000;"> e.toString(), e);
            }
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201806/612293-20180624170737100-200403851.png" alt=""></p>
</div>