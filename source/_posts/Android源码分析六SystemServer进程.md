---
title: Android 源码分析（六） SystemServer 进程
date: 2018-07-07 15:14:19
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/SystemServer.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>&nbsp;&nbsp;&nbsp; 一.前言:</h2>
<p>&nbsp;&nbsp;&nbsp; init进程 –&gt; Zygote进程 –&gt; SystemServer进程 –&gt; Launcher桌面程序 -&gt; 我们的App应用<br><br>&nbsp;&nbsp;&nbsp; init进程：linux的根进程，android系统是基于linux系统的，因此可以算作是整个android操作系统的第一个进程；<br><br>&nbsp;&nbsp;&nbsp; Zygote进程：android系统的根进程，主要作用：可以作用Zygote进程fork出SystemServer进程和各种应用进程；<br><br>&nbsp;&nbsp;&nbsp; <strong>SystemService进程：主要是在这个进程中启动系统的各项服务，比如ActivityManagerService，PackageManagerService，WindowManagerService服务等等；</strong><br>&nbsp;&nbsp; &nbsp;<br>&nbsp;&nbsp; &nbsp;Launcher桌面程序:就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序.<br>&nbsp;&nbsp;&nbsp; </p>
<h2>&nbsp; 二. SystemService进程</h2>
<p>&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; ZygoteInit类的main方法调用了StartSystemService() 启动SystemService.<br>&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;SystemServer进程主要的作用是启动各种系统服务，比如ActivityManagerService，PackageManagerService，WindowManagerService等服务.</p>
<p>&nbsp;</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteInit.java</span>
     <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Prepare the arguments and fork for the system server process.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> startSystemServer(String abiList, String socketName, ZygoteServer zygoteServer)
            </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> Zygote.MethodAndArgsCaller, RuntimeException {
        </span><span style="color: #0000ff;">long</span> capabilities =<span style="color: #000000;"> posixCapabilitiesAsBits(
            OsConstants.CAP_IPC_LOCK,
            OsConstants.CAP_KILL,
            OsConstants.CAP_NET_ADMIN,
            OsConstants.CAP_NET_BIND_SERVICE,
            OsConstants.CAP_NET_BROADCAST,
            OsConstants.CAP_NET_RAW,
            OsConstants.CAP_SYS_MODULE,
            OsConstants.CAP_SYS_NICE,
            OsConstants.CAP_SYS_PTRACE,
            OsConstants.CAP_SYS_TIME,
            OsConstants.CAP_SYS_TTY_CONFIG,
            OsConstants.CAP_WAKE_ALARM
        );
        </span><span style="color: #008000;">/*</span><span style="color: #008000;"> Containers run without this capability, so avoid setting it in that case </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">if</span> (!SystemProperties.getBoolean(PROPERTY_RUNNING_IN_CONTAINER, <span style="color: #0000ff;">false</span><span style="color: #000000;">)) {
            capabilities </span>|=<span style="color: #000000;"> posixCapabilitiesAsBits(OsConstants.CAP_BLOCK_SUSPEND);
        }
        </span><span style="color: #008000;">/*</span><span style="color: #008000;"> Hardcoded command line to start the system server </span><span style="color: #008000;">*/</span><span style="color: #000000;">
        String args[] </span>=<span style="color: #000000;"> {
            </span>"--setuid=1000"<span style="color: #000000;">,
            </span>"--setgid=1000"<span style="color: #000000;">,
            </span>"--setgroups=1001,1002,1003,1004,1005,1006,1007,1008,1009,1010,1018,1021,1023,1032,3001,3002,3003,3006,3007,3009,3010"<span style="color: #000000;">,
            </span>"--capabilities=" + capabilities + "," +<span style="color: #000000;"> capabilities,
            </span>"--nice-name=system_server"<span style="color: #000000;">,
            </span>"--runtime-args"<span style="color: #000000;">,
            </span>"com.android.server.SystemServer"<span style="color: #000000;">,
        };
        ZygoteConnection.Arguments parsedArgs </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;

        </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> pid;

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            parsedArgs </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ZygoteConnection.Arguments(args);
            ZygoteConnection.applyDebuggerSystemProperty(parsedArgs);
            ZygoteConnection.applyInvokeWithSystemProperty(parsedArgs);

            </span><span style="color: #008000;">/*</span><span style="color: #008000;"> Request to fork the system server process </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            pid </span>=<span style="color: #000000;"> Zygote.forkSystemServer(
                    parsedArgs.uid, parsedArgs.gid,
                    parsedArgs.gids,
                    parsedArgs.debugFlags,
                    </span><span style="color: #0000ff;">null</span><span style="color: #000000;">,
                    parsedArgs.permittedCapabilities,
                    parsedArgs.effectiveCapabilities);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IllegalArgumentException ex) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(ex);
        }

        </span><span style="color: #008000;">/*</span><span style="color: #008000;"> For child process </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">if</span> (pid == 0<span style="color: #000000;">) {
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (hasSecondZygote(abiList)) {
                waitForSecondaryZygote(socketName);
            }

            zygoteServer.closeServerSocket();
            handleSystemServerProcess(parsedArgs);
        }

        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp; 接上篇文章继续看看SystemServer在源码中做了什么.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">SystemServer.java</span>
    
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * The main entry point from zygote.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> main(String[] args) {
        </span><span style="color: #0000ff;">new</span><span style="color: #000000;"> SystemServer().run();
    }
    
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> run() {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            traceBeginAndSlog(</span>"InitBeforeStartServices"<span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If a device's clock is before 1970 (before 0), a lot of
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> APIs crash dealing with negative numbers, notably
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> java.io.File#setLastModified, so instead we fake it and
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> hope that time from cell towers or NTP fixes it shortly.</span>
            <span style="color: #0000ff;">if</span> (System.currentTimeMillis() &lt;<span style="color: #000000;"> EARLIEST_SUPPORTED_TIME) {
                Slog.w(TAG, </span>"System clock is before 1970; setting to 1970."<span style="color: #000000;">);
                SystemClock.setCurrentTimeMillis(EARLIEST_SUPPORTED_TIME);
            }

            </span><span style="color: #008000;">//</span>
            <span style="color: #008000;">//</span><span style="color: #008000;"> Default the timezone property to GMT if not set.
            </span><span style="color: #008000;">//
</span>            String timezoneProperty =  SystemProperties.get("persist.sys.timezone"<span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (timezoneProperty == <span style="color: #0000ff;">null</span> ||<span style="color: #000000;"> timezoneProperty.isEmpty()) {
                Slog.w(TAG, </span>"Timezone not set; setting to GMT."<span style="color: #000000;">);
                SystemProperties.set(</span>"persist.sys.timezone", "GMT"<span style="color: #000000;">);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the system has "persist.sys.language" and friends set, replace them with
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> "persist.sys.locale". Note that the default locale at this point is calculated
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> using the "-Duser.locale" command line flag. That flag is usually populated by
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> AndroidRuntime using the same set of system properties, but only the system_server
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> and system apps are allowed to set them.
            </span><span style="color: #008000;">//</span>
            <span style="color: #008000;">//</span><span style="color: #008000;"> NOTE: Most changes made here will need an equivalent change to
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> core/jni/AndroidRuntime.cpp</span>
            <span style="color: #0000ff;">if</span> (!SystemProperties.get("persist.sys.language"<span style="color: #000000;">).isEmpty()) {
                </span><span style="color: #0000ff;">final</span> String languageTag =<span style="color: #000000;"> Locale.getDefault().toLanguageTag();

                SystemProperties.set(</span>"persist.sys.locale"<span style="color: #000000;">, languageTag);
                SystemProperties.set(</span>"persist.sys.language", ""<span style="color: #000000;">);
                SystemProperties.set(</span>"persist.sys.country", ""<span style="color: #000000;">);
                SystemProperties.set(</span>"persist.sys.localevar", ""<span style="color: #000000;">);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> The system server should never make non-oneway calls</span>
            Binder.setWarnOnBlocking(<span style="color: #0000ff;">true</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Here we go!</span>
            Slog.i(TAG, "Entered the Android system server!"<span style="color: #000000;">);
            </span><span style="color: #0000ff;">int</span> uptimeMillis = (<span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime();
            EventLog.writeEvent(EventLogTags.BOOT_PROGRESS_SYSTEM_RUN, uptimeMillis);
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mRuntimeRestart) {
                MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, "boot_system_server_init"<span style="color: #000000;">, uptimeMillis);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> In case the runtime switched since last boot (such as when
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> the old runtime was removed in an OTA), set the system
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> property so that it is in sync. We can | xq oqi't do this in
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> libnativehelper's JniInvocation::Init code where we already
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> had to fallback to a different runtime because it is
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> running as root and we need to be the system user to set
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> the property. </span><span style="color: #008000; text-decoration: underline;">http://b/11463182</span>
            SystemProperties.set("persist.sys.dalvik.vm.lib.2"<span style="color: #000000;">, VMRuntime.getRuntime().vmLibrary());

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Enable the sampling profiler.</span>
            <span style="color: #0000ff;">if</span><span style="color: #000000;"> (SamplingProfilerIntegration.isEnabled()) {
                SamplingProfilerIntegration.start();
                mProfilerSnapshotTimer </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Timer();
                mProfilerSnapshotTimer.schedule(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> TimerTask() {
                        @Override
                        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> run() {
                            SamplingProfilerIntegration.writeSnapshot(</span>"system_server", <span style="color: #0000ff;">null</span><span style="color: #000000;">);
                        }
                    }, SNAPSHOT_INTERVAL, SNAPSHOT_INTERVAL);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Mmmmmm... more memory!</span>
<span style="color: #000000;">            VMRuntime.getRuntime().clearGrowthLimit();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> The system server has to run all of the time, so it needs to be
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> as efficient as possible with its memory usage.</span>
            VMRuntime.getRuntime().setTargetHeapUtilization(0.8f<span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Some devices rely on runtime fingerprint generation, so make sure
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> we've defined it before booting further.</span>
<span style="color: #000000;">            Build.ensureFingerprintProperty();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Within the system server, it is an error to access Environment paths without
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> explicitly specifying a user.</span>
            Environment.setUserRequired(<span style="color: #0000ff;">true</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Within the system server, any incoming Bundles should be defused
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> to avoid throwing BadParcelableException.</span>
            BaseBundle.setShouldDefuse(<span style="color: #0000ff;">true</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Ensure binder calls into the system always run at foreground priority.</span>
            BinderInternal.disableBackgroundScheduling(<span style="color: #0000ff;">true</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Increase the number of binder threads in system_server</span>
<span style="color: #000000;">            BinderInternal.setMaxThreads(sMaxBinderThreads);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Prepare the main looper thread (this thread).</span>
<span style="color: #000000;">            android.os.Process.setThreadPriority(
                android.os.Process.THREAD_PRIORITY_FOREGROUND);
            android.os.Process.setCanSelfBackground(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
            Looper.prepareMainLooper();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Initialize native services.</span>
            System.loadLibrary("android_servers"<span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Check whether we failed to shut down last time we tried.
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> This call may not return.</span>
<span style="color: #000000;">            performPendingShutdown();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Initialize the system context.</span>
<span style="color: #000000;">            createSystemContext();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Create the system service manager.</span>
            mSystemServiceManager = <span style="color: #0000ff;">new</span><span style="color: #000000;"> SystemServiceManager(mSystemContext);
            mSystemServiceManager.setRuntimeRestarted(mRuntimeRestart);
            LocalServices.addService(SystemServiceManager.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">, mSystemServiceManager);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Prepare the thread pool for init tasks that can be parallelized</span>
<span style="color: #000000;">            SystemServerInitThreadPool.get();
        } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
            traceEnd();  </span><span style="color: #008000;">//</span><span style="color: #008000;"> InitBeforeStartServices</span>
<span style="color: #000000;">        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Start services. 启动各种需要的服务</span>
        <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            traceBeginAndSlog(</span>"StartServices"<span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">启动系统Boot级服务 </span>
<span style="color: #000000;">            startBootstrapServices();
            </span><span style="color: #008000;">//</span><span style="color: #008000;">启动系统核心的服务 </span>
<span style="color: #000000;">            startCoreServices();
            </span><span style="color: #008000;">//</span><span style="color: #008000;">启动非核心的服务 </span>
<span style="color: #000000;">            startOtherServices();
            SystemServerInitThreadPool.shutdown();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Throwable ex) {
            Slog.e(</span>"System", "******************************************"<span style="color: #000000;">);
            Slog.e(</span>"System", "************ Failure starting system services"<span style="color: #000000;">, ex);
            </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> ex;
        } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
            traceEnd();
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> For debug builds, log event loop stalls to dropbox for analysis.</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (StrictMode.conditionallyEnableDebugLogging()) {
            Slog.i(TAG, </span>"Enabled StrictMode for system server main thread."<span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">if</span> (!mRuntimeRestart &amp;&amp; !<span style="color: #000000;">isFirstBootOrUpgrade()) {
            </span><span style="color: #0000ff;">int</span> uptimeMillis = (<span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime();
            MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, "boot_system_server_ready"<span style="color: #000000;">, uptimeMillis);
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> MAX_UPTIME_MILLIS = 60 * 1000<span style="color: #000000;">;
            </span><span style="color: #0000ff;">if</span> (uptimeMillis &gt;<span style="color: #000000;"> MAX_UPTIME_MILLIS) {
                Slog.wtf(SYSTEM_SERVER_TIMING_TAG,
                        </span>"SystemServer init took too long. uptimeMillis=" +<span style="color: #000000;"> uptimeMillis);
            }
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Loop forever.</span>
<span style="color: #000000;">        Looper.loop();
        </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Main thread loop unexpectedly exited"<span style="color: #000000;">);
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Starts the small tangle of critical services that are needed to get
     * the system off the ground.  These services have complex mutual dependencies
     * which is why we initialize them all in one place here.  Unless your service
     * is also entwined in these dependencies, it should be initialized in one of
     * the other functions.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> startBootstrapServices() {
        Slog.i(TAG, </span>"Reading configuration..."<span style="color: #000000;">);
        </span><span style="color: #0000ff;">final</span> String TAG_SYSTEM_CONFIG = "ReadingSystemConfig"<span style="color: #000000;">;
        traceBeginAndSlog(TAG_SYSTEM_CONFIG);
        SystemServerInitThreadPool.get().submit(SystemConfig::getInstance, TAG_SYSTEM_CONFIG);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Wait for installd to finish starting up so that it has a chance to
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> create critical directories such as /data/user with the appropriate
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> permissions.  We need this to complete before we initialize other services.</span>
        traceBeginAndSlog("StartInstaller"<span style="color: #000000;">);
        Installer installer </span>= mSystemServiceManager.startService(Installer.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> In some cases after launching an app we need to access device identifiers,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> therefore register the device identifier policy before the activity manager.</span>
        traceBeginAndSlog("DeviceIdentifiersPolicyService"<span style="color: #000000;">);
        mSystemServiceManager.startService(DeviceIdentifiersPolicyService.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Activity manager runs the show.</span>
        traceBeginAndSlog("StartActivityManager"<span style="color: #000000;">);
        mActivityManagerService </span>=<span style="color: #000000;"> mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">).getService();
        mActivityManagerService.setSystemServiceManager(mSystemServiceManager);
        mActivityManagerService.setInstaller(installer);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Power manager needs to be started early because other services need it.
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Native daemons may be watching for it to be registered so it must be ready
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> to handle incoming binder calls immediately (including being able to verify
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> the permissions for those calls).</span>
        traceBeginAndSlog("StartPowerManager"<span style="color: #000000;">);
        mPowerManagerService </span>= mSystemServiceManager.startService(PowerManagerService.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Now that the power manager has been started, let the activity manager
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> initialize power management features.</span>
        traceBeginAndSlog("InitPowerManagement"<span style="color: #000000;">);
        mActivityManagerService.initPowerManagement();
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Bring up recovery system in case a rescue party needs a reboot</span>
        <span style="color: #0000ff;">if</span> (!SystemProperties.getBoolean("config.disable_noncore", <span style="color: #0000ff;">false</span><span style="color: #000000;">)) {
            traceBeginAndSlog(</span>"StartRecoverySystemService"<span style="color: #000000;">);
            mSystemServiceManager.startService(RecoverySystemService.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
            traceEnd();
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Now that we have the bare essentials of the OS up and running, take
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> note that we just booted, which might send out a rescue party if
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> we're stuck in a runtime restart loop.</span>
<span style="color: #000000;">        RescueParty.noteBoot(mSystemContext);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Manages LEDs and display backlight so we need it to bring up the display.</span>
        traceBeginAndSlog("StartLightsService"<span style="color: #000000;">);
        mSystemServiceManager.startService(LightsService.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Display manager is needed to provide display metrics before package manager
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> starts up.</span>
        traceBeginAndSlog("StartDisplayManager"<span style="color: #000000;">);
        mDisplayManagerService </span>= mSystemServiceManager.startService(DisplayManagerService.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> We need the default display before we can initialize the package manager.</span>
        traceBeginAndSlog("WaitForDisplay"<span style="color: #000000;">);
        mSystemServiceManager.startBootPhase(SystemService.PHASE_WAIT_FOR_DEFAULT_DISPLAY);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Only run "core" apps if we're encrypting the device.</span>
        String cryptState = SystemProperties.get("vold.decrypt"<span style="color: #000000;">);
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (ENCRYPTING_STATE.equals(cryptState)) {
            Slog.w(TAG, </span>"Detected encryption in progress - only parsing core apps"<span style="color: #000000;">);
            mOnlyCore </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (ENCRYPTED_STATE.equals(cryptState)) {
            Slog.w(TAG, </span>"Device encrypted - only parsing core apps"<span style="color: #000000;">);
            mOnlyCore </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Start the package manager.</span>
        <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mRuntimeRestart) {
            MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, "boot_package_manager_init_start"<span style="color: #000000;">,
                    (</span><span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime());
        }
        traceBeginAndSlog(</span>"StartPackageManagerService"<span style="color: #000000;">);
        mPackageManagerService </span>=<span style="color: #000000;"> PackageManagerService.main(mSystemContext, installer,
                mFactoryTestMode </span>!=<span style="color: #000000;"> FactoryTest.FACTORY_TEST_OFF, mOnlyCore);
        mFirstBoot </span>=<span style="color: #000000;"> mPackageManagerService.isFirstBoot();
        mPackageManager </span>=<span style="color: #000000;"> mSystemContext.getPackageManager();
        traceEnd();
        </span><span style="color: #0000ff;">if</span> (!mRuntimeRestart &amp;&amp; !<span style="color: #000000;">isFirstBootOrUpgrade()) {
            MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, "boot_package_manager_init_ready"<span style="color: #000000;">,
                    (</span><span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime());
        }
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Manages A/B OTA dexopting. This is a bootstrap service as we need it to rename
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> A/B artifacts after boot, before anything else might touch/need them.
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Note: this isn't needed during decryption (we don't have /data anyways).</span>
        <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mOnlyCore) {
            </span><span style="color: #0000ff;">boolean</span> disableOtaDexopt = SystemProperties.getBoolean("config.disable_otadexopt"<span style="color: #000000;">,
                    </span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">disableOtaDexopt) {
                traceBeginAndSlog(</span>"StartOtaDexOptService"<span style="color: #000000;">);
                </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                    OtaDexoptService.main(mSystemContext, mPackageManagerService);
                } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Throwable e) {
                    reportWtf(</span>"starting OtaDexOptService"<span style="color: #000000;">, e);
                } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
                    traceEnd();
                }
            }
        }

        traceBeginAndSlog(</span>"StartUserManagerService"<span style="color: #000000;">);
        mSystemServiceManager.startService(UserManagerService.LifeCycle.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Initialize attribute cache used to cache resources from packages.</span>
        traceBeginAndSlog("InitAttributerCache"<span style="color: #000000;">);
        AttributeCache.init(mSystemContext);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up the Application instance for the system process and get started.</span>
        traceBeginAndSlog("SetSystemProcess"<span style="color: #000000;">);
        mActivityManagerService.setSystemProcess();
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> DisplayManagerService needs to setup android.display scheduling related policies
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> since setSystemProcess() would have overridden policies due to setProcessGroup</span>
<span style="color: #000000;">        mDisplayManagerService.setupSchedulerPolicies();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Manages Overlay packages</span>
        traceBeginAndSlog("StartOverlayManagerService"<span style="color: #000000;">);
        mSystemServiceManager.startService(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> OverlayManagerService(mSystemContext, installer));
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> The sensor service needs access to package manager service, app ops
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> service, and permissions service, therefore we start it after them.
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Start sensor service in a separate thread. Completion should be checked
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> before using it.</span>
        mSensorServiceStart = SystemServerInitThreadPool.get().submit(() -&gt;<span style="color: #000000;"> {
            BootTimingsTraceLog traceLog </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> BootTimingsTraceLog(
                    SYSTEM_SERVER_TIMING_ASYNC_TAG, Trace.TRACE_TAG_SYSTEM_SERVER);
            traceLog.traceBegin(START_SENSOR_SERVICE);
            startSensorService();
            traceLog.traceEnd();
        }, START_SENSOR_SERVICE);
    }

    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Starts some essential services that are not tangled up in the bootstrap process.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> startCoreServices() {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Records errors and logs, for example wtf()</span>
        traceBeginAndSlog("StartDropBoxManager"<span style="color: #000000;">);
        mSystemServiceManager.startService(DropBoxManagerService.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        traceBeginAndSlog(</span>"StartBatteryService"<span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Tracks the battery level.  Requires LightService.</span>
        mSystemServiceManager.startService(BatteryService.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Tracks application usage stats.</span>
        traceBeginAndSlog("StartUsageService"<span style="color: #000000;">);
        mSystemServiceManager.startService(UsageStatsService.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">);
        mActivityManagerService.setUsageStatsManager(
                LocalServices.getService(UsageStatsManagerInternal.</span><span style="color: #0000ff;">class</span><span style="color: #000000;">));
        traceEnd();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Tracks whether the updatable WebView is in a ready state and watches for update installs.</span>
        traceBeginAndSlog("StartWebViewUpdateService"<span style="color: #000000;">);
        mWebViewUpdateService </span>= mSystemServiceManager.startService(WebViewUpdateService.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
        traceEnd();
    }
    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> mSystemServiceManager是系统服务管理对象.在run()方法已经创建 mSystemServiceManager = new SystemServiceManager(mSystemContext);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 进入mSystemServiceManager.startService() 分析下. 进入具体实现的方法
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">SystemServiceManager.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Creates and starts a system service. The class must be a subclass of
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> com.android.server.SystemService}.
     *
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> serviceClass A Java class that implements the SystemService interface.
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> The service instance, never null.
     * </span><span style="color: #808080;">@throws</span><span style="color: #008000;"> RuntimeException if the service fails to start.
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @SuppressWarnings(</span>"unchecked"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span> &lt;T <span style="color: #0000ff;">extends</span> SystemService&gt; T startService(Class&lt;T&gt;<span style="color: #000000;"> serviceClass) {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">final</span> String name =<span style="color: #000000;"> serviceClass.getName();
            Slog.i(TAG, </span>"Starting " +<span style="color: #000000;"> name);
            Trace.traceBegin(Trace.TRACE_TAG_SYSTEM_SERVER, </span>"StartService " +<span style="color: #000000;"> name);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Create the service.</span>
            <span style="color: #0000ff;">if</span> (!SystemService.<span style="color: #0000ff;">class</span><span style="color: #000000;">.isAssignableFrom(serviceClass)) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create " +<span style="color: #000000;"> name
                        </span>+ ": service must extend " + SystemService.<span style="color: #0000ff;">class</span><span style="color: #000000;">.getName());
            }
            </span><span style="color: #0000ff;">final</span><span style="color: #000000;"> T service;
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                Constructor</span>&lt;T&gt; constructor = serviceClass.getConstructor(Context.<span style="color: #0000ff;">class</span><span style="color: #000000;">);
                service </span>=<span style="color: #000000;"> constructor.newInstance(mContext);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (InstantiationException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create service " +<span style="color: #000000;"> name
                        </span>+ ": service could not be instantiated"<span style="color: #000000;">, ex);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IllegalAccessException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create service " +<span style="color: #000000;"> name
                        </span>+ ": service must have a public constructor with a Context argument"<span style="color: #000000;">, ex);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (NoSuchMethodException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create service " +<span style="color: #000000;"> name
                        </span>+ ": service must have a public constructor with a Context argument"<span style="color: #000000;">, ex);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (InvocationTargetException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create service " +<span style="color: #000000;"> name
                        </span>+ ": service constructor threw an exception"<span style="color: #000000;">, ex);
            }

            startService(service);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> service;
        } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
            Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);
        }
    }
    
      </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Starts a service by class name.
     *
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> The service instance.
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @SuppressWarnings(</span>"unchecked"<span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> SystemService startService(String className) {
        </span><span style="color: #0000ff;">final</span> Class&lt;SystemService&gt;<span style="color: #000000;"> serviceClass;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            serviceClass </span>= (Class&lt;SystemService&gt;<span style="color: #000000;">)Class.forName(className);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ClassNotFoundException ex) {
            Slog.i(TAG, </span>"Starting " +<span style="color: #000000;"> className);
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to create service " +<span style="color: #000000;"> className
                    </span>+ ": service class not found, usually indicates that the caller should "
                    + "have called PackageManager.hasSystemFeature() to check whether the "
                    + "feature is available on this device before trying to start the "
                    + "services that implement it"<span style="color: #000000;">, ex);
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> startService(serviceClass);
    }
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">通过反射器构造方法创建出服务类,然后返回SystemService.启动对应的服务.</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>