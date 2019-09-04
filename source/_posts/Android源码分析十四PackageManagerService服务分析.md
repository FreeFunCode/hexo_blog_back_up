---
title: Android源码分析（十四）PackageManagerService服务分析
date: 2018-07-22 14:59:30
tags: 安卓源码
categories: 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/PackageManagerService.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <div>
<h3>一. PackageManagerService启动过程分析</h3>
&nbsp;PackageManagerService(PMS)主要是管理应用的安装，卸载，更新，解析以及权限。</div>
<div>　　<br>如果想了解SystemService启动过程请看这篇文章：<a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_2" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/SystemServer.html" target="_blank">Android 源码分析（六） SystemServer 进程</a><br>如果想了解AMS服务分析请看这篇文章：<a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/ActivityManagerService.html" target="_blank">Android源码分析（十三）ActivityManagerService服务分析</a></div>
<div>&nbsp;</div>
<div>同AMS一样，PMS也是由SystemServer启动的.</div>
<div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> final <span style="color: #0000ff;">class</span><span style="color: #000000;"> SystemServer {
    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> PackageManagerService mPackageManagerService;
    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> PackageManager mPackageManager;
    ...
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Start the package manager.
    </span><span style="color: #008000;">//</span><span style="color: #008000;">启动PMS服务</span>
    <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">mRuntimeRestart) {
        MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, <span style="color: #800000;">"</span><span style="color: #800000;">boot_package_manager_init_start</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                (</span><span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime());
    }
    traceBeginAndSlog(</span><span style="color: #800000;">"</span><span style="color: #800000;">StartPackageManagerService</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    mPackageManagerService </span>=<span style="color: #000000;"> PackageManagerService.main(mSystemContext, installer,
            mFactoryTestMode </span>!=<span style="color: #000000;"> FactoryTest.FACTORY_TEST_OFF, mOnlyCore);
    mFirstBoot </span>=<span style="color: #000000;"> mPackageManagerService.isFirstBoot();
    mPackageManager </span>=<span style="color: #000000;"> mSystemContext.getPackageManager();
    traceEnd();
    </span><span style="color: #0000ff;">if</span> (!mRuntimeRestart &amp;&amp; !<span style="color: #000000;">isFirstBootOrUpgrade()) {
        MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, <span style="color: #800000;">"</span><span style="color: #800000;">boot_package_manager_init_ready</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                (</span><span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime());
    }
    ...
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">PackageManagerService 初始化工作</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> PackageManagerService extends IPackageManager.Stub
        implements PackageSender {
        
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span><span style="color: #000000;"> PackageManagerService main(Context context, Installer installer,
            boolean factoryTest, boolean onlyCore) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Self-check for initial settings.</span>
<span style="color: #000000;">        PackageManagerServiceCompilerMapping.checkProperties();
        </span><span style="color: #008000;">//</span><span style="color: #008000;">构造一个PackageManagerService</span>
        PackageManagerService m = <span style="color: #0000ff;">new</span><span style="color: #000000;"> PackageManagerService(context, installer,
                factoryTest, onlyCore);
        m.enableSystemUserPackages();
        </span><span style="color: #008000;">//</span><span style="color: #008000;">添加到ServiceManager</span>
        ServiceManager.addService(<span style="color: #800000;">"</span><span style="color: #800000;">package</span><span style="color: #800000;">"</span><span style="color: #000000;">, m);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> m;
    }
    
    </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> PackageManagerService(Context context, Installer installer,
            boolean factoryTest, boolean onlyCore) {
        
        </span><span style="color: #008000;">//</span><span style="color: #008000;">installer apk的安装和卸载最终都是调用installd来实现的。</span>
        mInstaller =<span style="color: #000000;"> installer;
        mPackageDexOptimizer </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> PackageDexOptimizer(installer, mInstallLock, context,
                </span><span style="color: #800000;">"</span><span style="color: #800000;">*dexopt*</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        mDexManager </span>= <span style="color: #0000ff;">new</span> DexManager(<span style="color: #0000ff;">this</span><span style="color: #000000;">, mPackageDexOptimizer, installer, mInstallLock);
        mMoveCallbacks </span>= <span style="color: #0000ff;">new</span> MoveCallbacks(FgThread.<span style="color: #0000ff;">get</span><span style="color: #000000;">().getLooper());
        
        synchronized (mInstallLock) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> writer 
        </span><span style="color: #008000;">//</span><span style="color: #008000;">环境变量初始化</span>
<span style="color: #000000;">        synchronized (mPackages) {
            File dataDir </span>=<span style="color: #000000;"> Environment.getDataDirectory();
            mAppInstallDir </span>= <span style="color: #0000ff;">new</span> File(dataDir, <span style="color: #800000;">"</span><span style="color: #800000;">app</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            mAppLib32InstallDir </span>= <span style="color: #0000ff;">new</span> File(dataDir, <span style="color: #800000;">"</span><span style="color: #800000;">app-lib</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            mAsecInternalPath </span>= <span style="color: #0000ff;">new</span> File(dataDir, <span style="color: #800000;">"</span><span style="color: #800000;">app-asec</span><span style="color: #800000;">"</span><span style="color: #000000;">).getPath();
            mDrmAppPrivateInstallDir </span>= <span style="color: #0000ff;">new</span> File(dataDir, <span style="color: #800000;">"</span><span style="color: #800000;">app-private</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            sUserManager </span>= <span style="color: #0000ff;">new</span> UserManagerService(context, <span style="color: #0000ff;">this</span><span style="color: #000000;">,
                    </span><span style="color: #0000ff;">new</span><span style="color: #000000;"> UserDataPreparer(mInstaller, mInstallLock, mContext, mOnlyCore), mPackages);
                    
            
            </span><span style="color: #008000;">//</span><span style="color: #008000;">权限注册到 package manager，一个权限与几个组ID对应，当一个APK授予这个权限时，它同属于这几个组。
            </span><span style="color: #008000;">//</span><span style="color: #008000;">权限是一个复杂的过程
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Propagate permission configuration in to package manager.</span>
            ArrayMap&lt;String, SystemConfig.PermissionEntry&gt;<span style="color: #000000;"> permConfig
                    </span>=<span style="color: #000000;"> systemConfig.getPermissions();
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">遍历权限配置文件</span>
            <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i=<span style="color: #800080;">0</span>; i&lt;permConfig.size(); i++<span style="color: #000000;">) {
                SystemConfig.PermissionEntry perm </span>=<span style="color: #000000;"> permConfig.valueAt(i);
                </span><span style="color: #008000;">//</span><span style="color: #008000;">拿到权限</span>
                BasePermission bp = mSettings.mPermissions.<span style="color: #0000ff;">get</span><span style="color: #000000;">(perm.name);
                </span><span style="color: #0000ff;">if</span> (bp == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    bp </span>= <span style="color: #0000ff;">new</span> BasePermission(perm.name, <span style="color: #800000;">"</span><span style="color: #800000;">android</span><span style="color: #800000;">"</span><span style="color: #000000;">, BasePermission.TYPE_BUILTIN);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">写入权限</span>
<span style="color: #000000;">                    mSettings.mPermissions.put(perm.name, bp);
                }
                </span><span style="color: #0000ff;">if</span> (perm.gids != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    bp.setGids(perm.gids, perm.perUser);
                }
            }
        }
        
    final PackageHandler mHandler;
    </span><span style="color: #0000ff;">class</span><span style="color: #000000;"> PackageHandler extends Handler {
        </span><span style="color: #0000ff;">void</span><span style="color: #000000;"> doHandleMessage(Message msg) {
            </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (msg.what) {
            
            }
    }
    
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span> scanDirLI(File dir, <span style="color: #0000ff;">int</span> parseFlags, <span style="color: #0000ff;">int</span> scanFlags, <span style="color: #0000ff;">long</span><span style="color: #000000;"> currentTime) {
        final File[] files </span>=<span style="color: #000000;"> dir.listFiles();
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (ArrayUtils.isEmpty(files)) {
            Log.d(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">No files in app dir </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> dir);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
        }
        ParallelPackageParser parallelPackageParser </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ParallelPackageParser(
                mSeparateProcesses, mOnlyCore, mMetrics, mCacheDir,
                mParallelPackageParserCallback);

        parallelPackageParser.close();
    
    }
            
    File frameworkDir </span>= <span style="color: #0000ff;">new</span> File(Environment.getRootDirectory(), <span style="color: #800000;">"</span><span style="color: #800000;">framework</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Find base frameworks (resource packages without code).</span>
<span style="color: #000000;">    scanDirTracedLI(frameworkDir, mDefParseFlags
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_SYSTEM
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_SYSTEM_DIR
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_PRIVILEGED,
            scanFlags </span>| SCAN_NO_DEX, <span style="color: #800080;">0</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Collected privileged system packages. 系统安装包</span>
    final File privilegedAppDir = <span style="color: #0000ff;">new</span> File(Environment.getRootDirectory(), <span style="color: #800000;">"</span><span style="color: #800000;">priv-app</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    scanDirTracedLI(privilegedAppDir, mDefParseFlags
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_SYSTEM
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_SYSTEM_DIR
            </span>| PackageParser.PARSE_IS_PRIVILEGED, scanFlags, <span style="color: #800080;">0</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Collect ordinary system packages. 系统app安装包</span>
    final File systemAppDir = <span style="color: #0000ff;">new</span> File(Environment.getRootDirectory(), <span style="color: #800000;">"</span><span style="color: #800000;">app</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    scanDirTracedLI(systemAppDir, mDefParseFlags
            </span>|<span style="color: #000000;"> PackageParser.PARSE_IS_SYSTEM
            </span>| PackageParser.PARSE_IS_SYSTEM_DIR, scanFlags, <span style="color: #800080;">0</span><span style="color: #000000;">);
    
    
        
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　PMS里面主要完成以下几件事。<br>&nbsp;　　1、通过installer与installd进行连接，进行安装卸载应用操作<br>&nbsp;　　2、创建PacakageHandler线程，处理外部应用的安装卸载请求<br>&nbsp;　　3、处理系统权限相关配置<br>&nbsp;　　4、扫描安装应用，并解析APK安装包信息</p>
<div>
<h3>二.总结</h3>

&nbsp;一张图总结下PMS主要完成的工作，以及对上与PackageManager交互，向下与Installd的控制。<br>&nbsp;　　　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180722145529724-1218830986.png" alt=""></div>
<div><br>如果想了解桌面Launcher应用启动app过程，请看这篇文章。<a id="homepage1_HomePageDays_DaysList_ctl00_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/Launcher_app.html" target="_blank">Android 源码分析（八） Launcher 桌面启动App过程</a></div>
<div>最后补充一点，如果想要了解APK的编译过程，可以进一步去了解Android4.4之后使用的ART,可以与Dalivk对比了解。<br>给个Dalivk的启动过程介绍的文章：<a id="homepage1_HomePageDays_DaysList_ctl05_DayList_TitleUrl_0" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/dalvik.html" target="_blank">Android 源码分析（十） Dalvik 虚拟机创建过程</a></div>

</div>
<div>&nbsp;</div>
</div>