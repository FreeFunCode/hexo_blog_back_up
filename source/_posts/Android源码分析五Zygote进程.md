---
title: Android 源码分析（五） Zygote 进程
date: 2018-07-07 14:14:54
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/zygote.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.前言:&nbsp;&nbsp;</h2>
<p>&nbsp;&nbsp;&nbsp; init进程 –&gt; Zygote进程 –&gt; SystemServer进程 –&gt; Launcher桌面程序 -&gt; 我们的App应用<br><br>&nbsp;&nbsp;&nbsp; init进程：linux的根进程，android系统是基于linux系统的，因此可以算作是整个android操作系统的第一个进程；</p>
<h3>&nbsp;&nbsp;&nbsp; Zygote进程：android系统的根进程，主要作用：可以作用Zygote进程fork出SystemServer进程和各种应用进程；</h3>
<p>&nbsp;&nbsp;&nbsp; SystemService进程：主要是在这个进程中启动系统的各项服务，比如ActivityManagerService，PackageManagerService，WindowManagerService服务等等；<br>&nbsp;&nbsp; &nbsp;<br>&nbsp;&nbsp; &nbsp;Launcher桌面程序:就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序.&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;</p>
<h2>二. Zygote进程&nbsp;</h2>
<p>&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; Android系统是基于Linux内核的，而在Linux系统中，所有的进程都是init进程的子孙进程，也就是说，所有的进程都是直接或者间接地由init进程fork出来的。Zygote进程也不例外，它是在系统启动的过程，由init进程创建的.<br>&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; init进程在启动Zygote进程时一般都会调用ZygoteInit类的main方法.</p>
<p>　　如果SystemServer崩溃了，Zygote也会kill自己，重新创建进程服务。</p>
<p>&nbsp;　　</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteInit.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> main(String argv[]) {
        ZygoteServer zygoteServer </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ZygoteServer();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Mark zygote start. This ensures that thread creation will throw
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> an error.</span>
<span style="color: #000000;">        ZygoteHooks.startZygoteNoThreadCreation();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Zygote goes into its own process group.</span>
        <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            Os.setpgid(</span>0, 0<span style="color: #000000;">);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ErrnoException ex) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Failed to setpgid(0,0)"<span style="color: #000000;">, ex);
        }

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Report Zygote start time to tron unless it is a runtime restart</span>
            <span style="color: #0000ff;">if</span> (!"1".equals(SystemProperties.get("sys.boot_completed"<span style="color: #000000;">))) {
                MetricsLogger.histogram(</span><span style="color: #0000ff;">null</span>, "boot_zygote_init"<span style="color: #000000;">,
                        (</span><span style="color: #0000ff;">int</span><span style="color: #000000;">) SystemClock.elapsedRealtime());
            }

            String bootTimeTag </span>= Process.is64Bit() ? "Zygote64Timing" : "Zygote32Timing"<span style="color: #000000;">;
            BootTimingsTraceLog bootTimingsTraceLog </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> BootTimingsTraceLog(bootTimeTag,
                    Trace.TRACE_TAG_DALVIK);
            bootTimingsTraceLog.traceBegin(</span>"ZygoteInit"<span style="color: #000000;">);
            RuntimeInit.enableDdms();
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Start profiling the zygote initialization.</span>
<span style="color: #000000;">            SamplingProfilerIntegration.start();

            </span><span style="color: #0000ff;">boolean</span> startSystemServer = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            String socketName </span>= "zygote"<span style="color: #000000;">;
            String abiList </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">boolean</span> enableLazyPreload = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = 1; i &lt; argv.length; i++<span style="color: #000000;">) {
                </span><span style="color: #0000ff;">if</span> ("start-system-server"<span style="color: #000000;">.equals(argv[i])) {
                    startSystemServer </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> ("--enable-lazy-preload"<span style="color: #000000;">.equals(argv[i])) {
                    enableLazyPreload </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (argv[i].startsWith(ABI_LIST_ARG)) {
                    abiList </span>=<span style="color: #000000;"> argv[i].substring(ABI_LIST_ARG.length());
                } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;"> (argv[i].startsWith(SOCKET_NAME_ARG)) {
                    socketName </span>=<span style="color: #000000;"> argv[i].substring(SOCKET_NAME_ARG.length());
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Unknown command line argument: " +<span style="color: #000000;"> argv[i]);
                }
            }

            </span><span style="color: #0000ff;">if</span> (abiList == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("No ABI list supplied."<span style="color: #000000;">);
            }

            zygoteServer.registerServerSocket(socketName);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> In some configurations, we avoid preloading resources and classes eagerly.
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> In such cases, we will preload things prior to our first fork.</span>
            <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">enableLazyPreload) {
                bootTimingsTraceLog.traceBegin(</span>"ZygotePreload"<span style="color: #000000;">);
                EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_START,
                    SystemClock.uptimeMillis());
                preload(bootTimingsTraceLog);
                EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_END,
                    SystemClock.uptimeMillis());
                bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> ZygotePreload</span>
            } <span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                Zygote.resetNicePriority();
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Finish profiling the zygote initialization.</span>
<span style="color: #000000;">            SamplingProfilerIntegration.writeZygoteSnapshot();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Do an initial gc to clean up after startup</span>
            bootTimingsTraceLog.traceBegin("PostZygoteInitGC"<span style="color: #000000;">);
            gcAndFinalize();
            bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> PostZygoteInitGC</span>
<span style="color: #000000;">
            bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> ZygoteInit
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Disable tracing so that forked processes do not inherit stale tracing tags from
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Zygote.</span>
            Trace.setTracingEnabled(<span style="color: #0000ff;">false</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Zygote process unmounts root storage spaces.</span>
<span style="color: #000000;">            Zygote.nativeUnmountStorageOnInit();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set seccomp policy</span>
<span style="color: #000000;">            Seccomp.setPolicy();

            ZygoteHooks.stopZygoteNoThreadCreation();

            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (startSystemServer) {
                startSystemServer(abiList, socketName, zygoteServer);
            }

            Log.i(TAG, </span>"Accepting command socket connections"<span style="color: #000000;">);
            zygoteServer.runSelectLoop(abiList);

            zygoteServer.closeServerSocket();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Zygote.MethodAndArgsCaller caller) {
            caller.run();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Throwable ex) {
            Log.e(TAG, </span>"System zygote died with exception"<span style="color: #000000;">, ex);
            zygoteServer.closeServerSocket();
            </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> ex;
        }
    }
    </span><span style="color: #008000;">//</span><span style="color: #008000;">1.registerServerSocket
    </span><span style="color: #008000;">//</span><span style="color: #008000;">2.调用preload加载资源，
    </span><span style="color: #008000;">//</span><span style="color: #008000;">3.利用gcAndFinalize初始化gc，
    </span><span style="color: #008000;">//</span><span style="color: #008000;">4.启动SystemServer，startSystemServer() 这里启动SystemServer服务.
    </span><span style="color: #008000;">//</span><span style="color: #008000;">5.调用runSelectLoop运行Zygote进程选择的looper，
    </span><span style="color: #008000;">//</span><span style="color: #008000;">6.关闭和清理zygote sockets </span>
    </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteServer.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Registers a server socket for zygote command connections
     *
     * </span><span style="color: #808080;">@throws</span><span style="color: #008000;"> RuntimeException when open fails
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">void</span><span style="color: #000000;"> registerServerSocket(String socketName) {
        </span><span style="color: #0000ff;">if</span> (mServerSocket == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> fileDesc;
            </span><span style="color: #0000ff;">final</span> String fullSocketName = ANDROID_SOCKET_PREFIX +<span style="color: #000000;"> socketName;
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                String env </span>=<span style="color: #000000;"> System.getenv(fullSocketName);
                fileDesc </span>=<span style="color: #000000;"> Integer.parseInt(env);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (RuntimeException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException(fullSocketName + " unset or invalid"<span style="color: #000000;">, ex);
            }

            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                FileDescriptor fd </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> FileDescriptor();
                fd.setInt$(fileDesc);
                mServerSocket </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> LocalServerSocket(fd);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(
                        </span>"Error binding to local socket '" + fileDesc + "'"<span style="color: #000000;">, ex);
            }
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteInit.java</span>
    <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> preload(BootTimingsTraceLog bootTimingsTraceLog) {
        Log.d(TAG, </span>"begin preload"<span style="color: #000000;">);
        bootTimingsTraceLog.traceBegin(</span>"BeginIcuCachePinning"<span style="color: #000000;">);
        beginIcuCachePinning();
        bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> BeginIcuCachePinning</span>
        bootTimingsTraceLog.traceBegin("PreloadClasses"<span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化Zygote中需要的class类        </span>
<span style="color: #000000;">        preloadClasses();
        bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> PreloadClasses</span>
        bootTimingsTraceLog.traceBegin("PreloadResources"<span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化系统资源</span>
<span style="color: #000000;">        preloadResources();
        bootTimingsTraceLog.traceEnd(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> PreloadResources</span>
        Trace.traceBegin(Trace.TRACE_TAG_DALVIK, "PreloadOpenGL"<span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化OpenGL</span>
<span style="color: #000000;">        preloadOpenGL();
        Trace.traceEnd(Trace.TRACE_TAG_DALVIK);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化系统libraries</span>
<span style="color: #000000;">        preloadSharedLibraries();
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化文字资源</span>
<span style="color: #000000;">        preloadTextResources();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Ask the WebViewFactory to do any initialization that must run in the zygote process,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> for memory sharing purposes.
        </span><span style="color: #008000;">//</span><span style="color: #008000;">初始化webview</span>
<span style="color: #000000;">        WebViewFactory.prepareWebViewInZygote();
        endIcuCachePinning();
        warmUpJcaProviders();
        Log.d(TAG, </span>"end preload"<span style="color: #000000;">);

        sPreloadComplete </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">进去看看初始化webview方法
    </span><span style="color: #008000;">//</span><span style="color: #008000;">WebViewFactory.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Perform any WebView loading preparations that must happen in the zygote.
     * Currently, this means allocating address space to load the real JNI library later.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> prepareWebViewInZygote() {
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            System.loadLibrary(</span>"webviewchromium_loader"<span style="color: #000000;">);
            </span><span style="color: #0000ff;">long</span> addressSpaceToReserve =<span style="color: #000000;">
                    SystemProperties.getLong(CHROMIUM_WEBVIEW_VMSIZE_SIZE_PROPERTY,
                    CHROMIUM_WEBVIEW_DEFAULT_VMSIZE_BYTES);
            sAddressSpaceReserved </span>=<span style="color: #000000;"> nativeReserveAddressSpace(addressSpaceToReserve);

            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (sAddressSpaceReserved) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (DEBUG) {
                    Log.v(LOGTAG, </span>"address space reserved: " + addressSpaceToReserve + " bytes"<span style="color: #000000;">);
                }
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                Log.e(LOGTAG, </span>"reserving " + addressSpaceToReserve +
                        " bytes of address space failed"<span style="color: #000000;">);
            }
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Throwable t) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Log and discard errors at this stage as we must not crash the zygote.</span>
            Log.e(LOGTAG, "error preparing native loader"<span style="color: #000000;">, t);
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>