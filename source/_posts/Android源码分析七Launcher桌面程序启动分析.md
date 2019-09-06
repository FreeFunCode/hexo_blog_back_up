---
title: Android 源码分析（七） Launcher 桌面程序启动分析
date: 2018-07-07 19:51:44
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/Launcher.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.前言:</h2>
<p>&nbsp;&nbsp;&nbsp; init进程 –&gt; Zygote进程 –&gt; SystemServer进程 –&gt; Launcher桌面程序 -&gt; 我们的App应用<br><br>&nbsp;&nbsp;&nbsp; init进程：linux的根进程，android系统是基于linux系统的，因此可以算作是整个android操作系统的第一个进程；<br><br>&nbsp;&nbsp;&nbsp; Zygote进程：android系统的根进程，主要作用：可以作用Zygote进程fork出SystemServer进程和各种应用进程；<br><br>&nbsp;&nbsp;&nbsp; SystemService进程：主要是在这个进程中启动系统的各项服务，比如ActivityManagerService，PackageManagerService，WindowManagerService服务等等；<br>&nbsp;&nbsp; &nbsp;<br>&nbsp;&nbsp; &nbsp;<strong>Launcher桌面程序:就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序</strong>.<strong>在Zygote进程里等待SystemService进程启动后，创建.</strong><br>&nbsp;&nbsp;&nbsp; </p>
<h2>&nbsp; 二. Launcher 桌面程序启动</h2>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Launcher程序就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序。</p>
<p>　　首先了解下，Zygote进程是如何启动一个应用的。</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180708235023116-19887742.png" alt=""></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteServer.java</span>
 <span style="color: #0000ff;">void</span> runSelectLoop(String abiList) <span style="color: #0000ff;">throws</span><span style="color: #000000;"> Zygote.MethodAndArgsCaller {
        ArrayList</span>&lt;FileDescriptor&gt; fds = <span style="color: #0000ff;">new</span> ArrayList&lt;FileDescriptor&gt;<span style="color: #000000;">();
        ArrayList</span>&lt;ZygoteConnection&gt; peers = <span style="color: #0000ff;">new</span> ArrayList&lt;ZygoteConnection&gt;<span style="color: #000000;">();

        fds.add(mServerSocket.getFileDescriptor());
        peers.add(</span><span style="color: #0000ff;">null</span><span style="color: #000000;">);

        </span><span style="color: #0000ff;">while</span> (<span style="color: #0000ff;">true</span><span style="color: #000000;">) {
            StructPollfd[] pollFds </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> StructPollfd[fds.size()];
            </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = 0; i &lt; pollFds.length; ++<span style="color: #000000;">i) {
                pollFds[i] </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> StructPollfd();
                pollFds[i].fd </span>=<span style="color: #000000;"> fds.get(i);
                pollFds[i].events </span>= (<span style="color: #0000ff;">short</span><span style="color: #000000;">) POLLIN;
            }
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                Os.poll(pollFds, </span>-1<span style="color: #000000;">);
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ErrnoException ex) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("poll failed"<span style="color: #000000;">, ex);
            }
            </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = pollFds.length - 1; i &gt;= 0; --<span style="color: #000000;">i) {
                </span><span style="color: #0000ff;">if</span> ((pollFds[i].revents &amp; POLLIN) == 0<span style="color: #000000;">) {
                    </span><span style="color: #0000ff;">continue</span><span style="color: #000000;">;
                }
                </span><span style="color: #0000ff;">if</span> (i == 0<span style="color: #000000;">) {
                    ZygoteConnection newPeer </span>=<span style="color: #000000;"> acceptCommandPeer(abiList);
                    peers.add(newPeer);
                    fds.add(newPeer.getFileDesciptor());
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    </span><span style="color: #0000ff;">boolean</span> done = peers.get(i).runOnce(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
                    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (done) {
                        peers.remove(i);
                        fds.remove(i);
                    }
                }
            }
        }
    }
}<br><br>　　/**<br>&nbsp;&nbsp;&nbsp;&nbsp; * Waits for and accepts a single command connection. Throws<br>&nbsp;&nbsp;&nbsp;&nbsp; * RuntimeException on failure.<br>&nbsp;&nbsp;&nbsp;&nbsp; */<br>&nbsp;&nbsp;&nbsp; private ZygoteConnection acceptCommandPeer(String abiList) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; try {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; return createNewConnection(mServerSocket.accept(), abiList);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; } catch (IOException ex) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; throw new RuntimeException(<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; "IOException during accept()", ex);<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br>&nbsp;&nbsp;&nbsp; }<br>&nbsp;<br>&nbsp;protected ZygoteConnection createNewConnection(LocalSocket socket, String abiList)<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; throws IOException {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; return new ZygoteConnection(socket, abiList);<br>&nbsp;&nbsp;&nbsp; }<br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code" onclick="cnblogs_code_show('b4582d4a-5eee-42a1-a93e-5858a6f4d479')"><img id="code_img_closed_b4582d4a-5eee-42a1-a93e-5858a6f4d479" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_b4582d4a-5eee-42a1-a93e-5858a6f4d479" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('b4582d4a-5eee-42a1-a93e-5858a6f4d479',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_b4582d4a-5eee-42a1-a93e-5858a6f4d479" class="cnblogs_code_hide">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteConnection.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Constructs instance from connected socket.
     *
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> socket non-null; connected socket
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> abiList non-null; a list of ABIs this zygote supports.
     * </span><span style="color: #808080;">@throws</span><span style="color: #008000;"> IOException
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    ZygoteConnection(LocalSocket socket, String abiList) </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> IOException {
        mSocket </span>=<span style="color: #000000;"> socket;
        </span><span style="color: #0000ff;">this</span>.abiList =<span style="color: #000000;"> abiList;

        mSocketOutStream
                </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> DataOutputStream(socket.getOutputStream());

        mSocketReader </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> BufferedReader(
                </span><span style="color: #0000ff;">new</span> InputStreamReader(socket.getInputStream()), 256<span style="color: #000000;">);

        mSocket.setSoTimeout(CONNECTION_TIMEOUT_MILLIS);

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            peer </span>=<span style="color: #000000;"> mSocket.getPeerCredentials();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException ex) {
            Log.e(TAG, </span>"Cannot read peer credentials"<span style="color: #000000;">, ex);
            </span><span style="color: #0000ff;">throw</span><span style="color: #000000;"> ex;
        }
    }
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Reads one start command from the command socket. If successful,
     * a child is forked and a {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> Zygote.MethodAndArgsCaller}
     * exception is thrown in that child while in the parent process,
     * the method returns normally. On failure, the child is not
     * spawned and messages are printed to the log and stderr. Returns
     * a boolean status value indicating whether an end-of-file on the command
     * socket has been encountered.
     *
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> false if command socket should continue to be read from, or
     * true if an end-of-file has been encountered.
     * </span><span style="color: #808080;">@throws</span><span style="color: #008000;"> Zygote.MethodAndArgsCaller trampoline to invoke main()
     * method in child process
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">boolean</span> runOnce(ZygoteServer zygoteServer) <span style="color: #0000ff;">throws</span><span style="color: #000000;"> Zygote.MethodAndArgsCaller {

        String args[];
        Arguments parsedArgs </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        FileDescriptor[] descriptors;

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            args </span>=<span style="color: #000000;"> readArgumentList();
            descriptors </span>=<span style="color: #000000;"> mSocket.getAncillaryFileDescriptors();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException ex) {
            Log.w(TAG, </span>"IOException on command socket " +<span style="color: #000000;"> ex.getMessage());
            closeSocket();
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }

        </span><span style="color: #0000ff;">if</span> (args == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> EOF reached.</span>
<span style="color: #000000;">            closeSocket();
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        }

        </span><span style="color: #008000;">/**</span><span style="color: #008000;"> the stderr of the most recent request, if avail </span><span style="color: #008000;">*/</span><span style="color: #000000;">
        PrintStream newStderr </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;

        </span><span style="color: #0000ff;">if</span> (descriptors != <span style="color: #0000ff;">null</span> &amp;&amp; descriptors.length &gt;= 3<span style="color: #000000;">) {
            newStderr </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> PrintStream(
                    </span><span style="color: #0000ff;">new</span> FileOutputStream(descriptors[2<span style="color: #000000;">]));
        }

        </span><span style="color: #0000ff;">int</span> pid = -1<span style="color: #000000;">;
        FileDescriptor childPipeFd </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        FileDescriptor serverPipeFd </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            parsedArgs </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Arguments(args);

            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (parsedArgs.abiListQuery) {
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> handleAbiListQuery();
            }

            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (parsedArgs.preloadDefault) {
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> handlePreload();
            }

            </span><span style="color: #0000ff;">if</span> (parsedArgs.preloadPackage != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> handlePreloadPackage(parsedArgs.preloadPackage,
                        parsedArgs.preloadPackageLibs, parsedArgs.preloadPackageCacheKey);
            }

            </span><span style="color: #0000ff;">if</span> (parsedArgs.permittedCapabilities != 0 || parsedArgs.effectiveCapabilities != 0<span style="color: #000000;">) {
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> ZygoteSecurityException("Client may not specify capabilities: " +
                        "permitted=0x" + Long.toHexString(parsedArgs.permittedCapabilities) +
                        ", effective=0x" +<span style="color: #000000;"> Long.toHexString(parsedArgs.effectiveCapabilities));
            }

            applyUidSecurityPolicy(parsedArgs, peer);
            applyInvokeWithSecurityPolicy(parsedArgs, peer);

            applyDebuggerSystemProperty(parsedArgs);
            applyInvokeWithSystemProperty(parsedArgs);

            </span><span style="color: #0000ff;">int</span>[][] rlimits = <span style="color: #0000ff;">null</span><span style="color: #000000;">;

            </span><span style="color: #0000ff;">if</span> (parsedArgs.rlimits != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                rlimits </span>=<span style="color: #000000;"> parsedArgs.rlimits.toArray(intArray2d);
            }

            </span><span style="color: #0000ff;">int</span>[] fdsToIgnore = <span style="color: #0000ff;">null</span><span style="color: #000000;">;

            </span><span style="color: #0000ff;">if</span> (parsedArgs.invokeWith != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                FileDescriptor[] pipeFds </span>=<span style="color: #000000;"> Os.pipe2(O_CLOEXEC);
                childPipeFd </span>= pipeFds[1<span style="color: #000000;">];
                serverPipeFd </span>= pipeFds[0<span style="color: #000000;">];
                Os.fcntlInt(childPipeFd, F_SETFD, </span>0<span style="color: #000000;">);
                fdsToIgnore </span>= <span style="color: #0000ff;">new</span> <span style="color: #0000ff;">int</span><span style="color: #000000;">[] { childPipeFd.getInt$(), serverPipeFd.getInt$() };
            }

            </span><span style="color: #008000;">/**</span><span style="color: #008000;">
             * In order to avoid leaking descriptors to the Zygote child,
             * the native code must close the two Zygote socket descriptors
             * in the child process before it switches from Zygote-root to
             * the UID and privileges of the application being launched.
             *
             * In order to avoid "bad file descriptor" errors when the
             * two LocalSocket objects are closed, the Posix file
             * descriptors are released via a dup2() call which closes
             * the socket and substitutes an open descriptor to /dev/null.
             </span><span style="color: #008000;">*/</span>

            <span style="color: #0000ff;">int</span> [] fdsToClose = { -1, -1<span style="color: #000000;"> };

            FileDescriptor fd </span>=<span style="color: #000000;"> mSocket.getFileDescriptor();

            </span><span style="color: #0000ff;">if</span> (fd != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                fdsToClose[</span>0] =<span style="color: #000000;"> fd.getInt$();
            }

            fd </span>=<span style="color: #000000;"> zygoteServer.getServerSocketFileDescriptor();

            </span><span style="color: #0000ff;">if</span> (fd != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                fdsToClose[</span>1] =<span style="color: #000000;"> fd.getInt$();
            }

            fd </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;

            pid </span>=<span style="color: #000000;"> Zygote.forkAndSpecialize(parsedArgs.uid, parsedArgs.gid, parsedArgs.gids,
                    parsedArgs.debugFlags, rlimits, parsedArgs.mountExternal, parsedArgs.seInfo,
                    parsedArgs.niceName, fdsToClose, fdsToIgnore, parsedArgs.instructionSet,
                    parsedArgs.appDataDir);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ErrnoException ex) {
            logAndPrintError(newStderr, </span>"Exception creating pipe"<span style="color: #000000;">, ex);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IllegalArgumentException ex) {
            logAndPrintError(newStderr, </span>"Invalid zygote arguments"<span style="color: #000000;">, ex);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ZygoteSecurityException ex) {
            logAndPrintError(newStderr,
                    </span>"Zygote security policy prevents request: "<span style="color: #000000;">, ex);
        }

        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span> (pid == 0<span style="color: #000000;">) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> in child</span>
<span style="color: #000000;">                zygoteServer.closeServerSocket();
                IoUtils.closeQuietly(serverPipeFd);
                serverPipeFd </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                handleChildProc(parsedArgs, descriptors, childPipeFd, newStderr);

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> should never get here, the child is expected to either
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> throw Zygote.MethodAndArgsCaller or exec().</span>
                <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> in parent...pid of &lt; 0 means failure</span>
<span style="color: #000000;">                IoUtils.closeQuietly(childPipeFd);
                childPipeFd </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> handleParentProc(pid, descriptors, serverPipeFd, parsedArgs);
            }
        } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
            IoUtils.closeQuietly(childPipeFd);
            IoUtils.closeQuietly(serverPipeFd);
        }
    }
    </span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<div class="cnblogs_code" onclick="cnblogs_code_show('24047af0-fb9d-4495-955a-61ae30e0b7ac')"><img id="code_img_closed_24047af0-fb9d-4495-955a-61ae30e0b7ac" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_24047af0-fb9d-4495-955a-61ae30e0b7ac" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('24047af0-fb9d-4495-955a-61ae30e0b7ac',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_24047af0-fb9d-4495-955a-61ae30e0b7ac" class="cnblogs_code_hide">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">Zygote.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">int</span> forkAndSpecialize(<span style="color: #0000ff;">int</span> uid, <span style="color: #0000ff;">int</span> gid, <span style="color: #0000ff;">int</span>[] gids, <span style="color: #0000ff;">int</span><span style="color: #000000;"> debugFlags,
          </span><span style="color: #0000ff;">int</span>[][] rlimits, <span style="color: #0000ff;">int</span> mountExternal, String seInfo, String niceName, <span style="color: #0000ff;">int</span><span style="color: #000000;">[] fdsToClose,
          </span><span style="color: #0000ff;">int</span><span style="color: #000000;">[] fdsToIgnore, String instructionSet, String appDataDir) {
        VM_HOOKS.preFork();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Resets nice priority for zygote process.</span>
<span style="color: #000000;">        resetNicePriority();
        </span><span style="color: #0000ff;">int</span> pid =<span style="color: #000000;"> nativeForkAndSpecialize(
                  uid, gid, gids, debugFlags, rlimits, mountExternal, seInfo, niceName, fdsToClose,
                  fdsToIgnore, instructionSet, appDataDir);
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Enable tracing as soon as possible for the child process.</span>
        <span style="color: #0000ff;">if</span> (pid == 0<span style="color: #000000;">) {
            Trace.setTracingEnabled(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Note that this event ends at the end of handleChildProc,</span>
            Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "PostFork"<span style="color: #000000;">);
        }
        VM_HOOKS.postForkCommon();
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> pid;
    }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<div class="cnblogs_code" onclick="cnblogs_code_show('585017ee-5c61-4dd0-b78d-a900abceb71c')"><img id="code_img_closed_585017ee-5c61-4dd0-b78d-a900abceb71c" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_585017ee-5c61-4dd0-b78d-a900abceb71c" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('585017ee-5c61-4dd0-b78d-a900abceb71c',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_585017ee-5c61-4dd0-b78d-a900abceb71c" class="cnblogs_code_hide">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ZygoteConnection.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Handles post-fork setup of child proc, closing sockets as appropriate,
     * reopen stdio as appropriate, and ultimately throwing MethodAndArgsCaller
     * if successful or returning if failed.
     *
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> parsedArgs non-null; zygote args
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> descriptors null-ok; new file descriptors for stdio if available.
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> pipeFd null-ok; pipe for communication back to Zygote.
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> newStderr null-ok; stream to use for stderr until stdio
     * is reopened.
     *
     * </span><span style="color: #808080;">@throws</span><span style="color: #008000;"> Zygote.MethodAndArgsCaller on success to
     * trampoline to code that invokes static main.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> handleChildProc(Arguments parsedArgs,
            FileDescriptor[] descriptors, FileDescriptor pipeFd, PrintStream newStderr)
            </span><span style="color: #0000ff;">throws</span><span style="color: #000000;"> Zygote.MethodAndArgsCaller {
        </span><span style="color: #008000;">/**</span><span style="color: #008000;">
         * By the time we get here, the native code has closed the two actual Zygote
         * socket connections, and substituted /dev/null in their place.  The LocalSocket
         * objects still need to be closed properly.
         </span><span style="color: #008000;">*/</span><span style="color: #000000;">

        closeSocket();
        </span><span style="color: #0000ff;">if</span> (descriptors != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                Os.dup2(descriptors[</span>0<span style="color: #000000;">], STDIN_FILENO);
                Os.dup2(descriptors[</span>1<span style="color: #000000;">], STDOUT_FILENO);
                Os.dup2(descriptors[</span>2<span style="color: #000000;">], STDERR_FILENO);

                </span><span style="color: #0000ff;">for</span><span style="color: #000000;"> (FileDescriptor fd: descriptors) {
                    IoUtils.closeQuietly(fd);
                }
                newStderr </span>=<span style="color: #000000;"> System.err;
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (ErrnoException ex) {
                Log.e(TAG, </span>"Error reopening stdio"<span style="color: #000000;">, ex);
            }
        }

        </span><span style="color: #0000ff;">if</span> (parsedArgs.niceName != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            Process.setArgV0(parsedArgs.niceName);
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> End of the postFork event.</span>
<span style="color: #000000;">        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        </span><span style="color: #0000ff;">if</span> (parsedArgs.invokeWith != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            WrapperInit.execApplication(parsedArgs.invokeWith,
                    parsedArgs.niceName, parsedArgs.targetSdkVersion,
                    VMRuntime.getCurrentInstructionSet(),
                    pipeFd, parsedArgs.remainingArgs);
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            ZygoteInit.zygoteInit(parsedArgs.targetSdkVersion,
                    parsedArgs.remainingArgs, </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;"> classLoader </span><span style="color: #008000;">*/</span><span style="color: #000000;">);
        }
    }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>&nbsp;</p>
<p>　　前一篇文章我们知道在SystemServer进程的启动过程中会调用其main静态方法，开始执行整个SystemServer的启动流程。在调用startOtherService方法中就会通过调用mActivityManagerService.systemReady()方法。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">SystemServer.java</span>
<span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> startOtherServices() {
    ......
         </span><span style="color: #008000;">//</span><span style="color: #008000;"> We now tell the activity manager it is okay to run third party
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> code.  It will call back into us once it has gotten to the state
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> where third party code can really run (but before it has actually
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> started launching the initial applications), for us to complete our
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> initialization.</span>
<span style="color: #000000;">        mActivityManagerService.systemReady()
    ......
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ActivityManagerService.java</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> systemReady(<span style="color: #0000ff;">final</span><span style="color: #000000;"> Runnable goingCallback, BootTimingsTraceLog traceLog) {
        ......
    startHomeActivityLocked(currentUserId, </span>"systemReady"<span style="color: #000000;">);
        ......

}

</span><span style="color: #0000ff;">boolean</span> startHomeActivityLocked(<span style="color: #0000ff;">int</span><span style="color: #000000;"> userId, String reason) {
        </span><span style="color: #0000ff;">if</span> (mFactoryTest ==<span style="color: #000000;"> FactoryTest.FACTORY_TEST_LOW_LEVEL
                </span>&amp;&amp; mTopAction == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> We are running in factory test mode, but unable to find
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> the factory test app, so just sit around displaying the
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> error message and don't try to start anything.</span>
            <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        }
        Intent intent </span>=<span style="color: #000000;"> getHomeIntent();
        ActivityInfo aInfo </span>=<span style="color: #000000;"> resolveActivityInfo(intent, STOCK_PM_FLAGS, userId);
        </span><span style="color: #0000ff;">if</span> (aInfo != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            intent.setComponent(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> ComponentName(aInfo.applicationInfo.packageName, aInfo.name));
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Don't do this if the home app is currently being
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> instrumented.</span>
            aInfo = <span style="color: #0000ff;">new</span><span style="color: #000000;"> ActivityInfo(aInfo);
            aInfo.applicationInfo </span>=<span style="color: #000000;"> getAppInfoForUser(aInfo.applicationInfo, userId);
            ProcessRecord app </span>=<span style="color: #000000;"> getProcessRecordLocked(aInfo.processName,
                    aInfo.applicationInfo.uid, </span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (app == <span style="color: #0000ff;">null</span> || app.instr == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                intent.setFlags(intent.getFlags() </span>|<span style="color: #000000;"> Intent.FLAG_ACTIVITY_NEW_TASK);
                </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> resolvedUserId =<span style="color: #000000;"> UserHandle.getUserId(aInfo.applicationInfo.uid);
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> For ANR debugging to verify if the user activity is the one that actually
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> launched.</span>
                <span style="color: #0000ff;">final</span> String myReason = reason + ":" + userId + ":" +<span style="color: #000000;"> resolvedUserId;
                mActivityStarter.startHomeActivityLocked(intent, aInfo, myReason);
            }
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            Slog.wtf(TAG, </span>"No home screen found for " + intent, <span style="color: #0000ff;">new</span><span style="color: #000000;"> Throwable());
        }

        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
    }

     Intent getHomeIntent() {
        Intent intent </span>= <span style="color: #0000ff;">new</span> Intent(mTopAction, mTopData != <span style="color: #0000ff;">null</span> ? Uri.parse(mTopData) : <span style="color: #0000ff;">null</span><span style="color: #000000;">);
        intent.setComponent(mTopComponent);
        intent.addFlags(Intent.FLAG_DEBUG_TRIAGED_MISSING);
        </span><span style="color: #0000ff;">if</span> (mFactoryTest !=<span style="color: #000000;"> FactoryTest.FACTORY_TEST_LOW_LEVEL) {
            intent.addCategory(Intent.CATEGORY_HOME);
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> intent;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">ActivityStarter.java</span>
    <span style="color: #0000ff;">void</span><span style="color: #000000;"> startHomeActivityLocked(Intent intent, ActivityInfo aInfo, String reason) {
        mSupervisor.moveHomeStackTaskToTop(reason);
        mLastHomeActivityStartResult </span>= startActivityLocked(<span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">caller</span><span style="color: #008000;">*/</span><span style="color: #000000;">, intent,
                </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">ephemeralIntent</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">resolvedType</span><span style="color: #008000;">*/</span>, aInfo, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">rInfo</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">voiceSession</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">voiceInteractor</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">resultTo</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">resultWho</span><span style="color: #008000;">*/</span>, 0 <span style="color: #008000;">/*</span><span style="color: #008000;">requestCode</span><span style="color: #008000;">*/</span>, 0 <span style="color: #008000;">/*</span><span style="color: #008000;">callingPid</span><span style="color: #008000;">*/</span>, 0 <span style="color: #008000;">/*</span><span style="color: #008000;">callingUid</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">callingPackage</span><span style="color: #008000;">*/</span>, 0 <span style="color: #008000;">/*</span><span style="color: #008000;">realCallingPid</span><span style="color: #008000;">*/</span>, 0 <span style="color: #008000;">/*</span><span style="color: #008000;">realCallingUid</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span>0 <span style="color: #008000;">/*</span><span style="color: #008000;">startFlags</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">options</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">false</span> <span style="color: #008000;">/*</span><span style="color: #008000;">ignoreTargetSecurity</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span><span style="color: #0000ff;">false</span> <span style="color: #008000;">/*</span><span style="color: #008000;">componentSpecified</span><span style="color: #008000;">*/</span>, mLastHomeActivityStartRecord <span style="color: #008000;">/*</span><span style="color: #008000;">outActivity</span><span style="color: #008000;">*/</span><span style="color: #000000;">,
                </span><span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">container</span><span style="color: #008000;">*/</span>, <span style="color: #0000ff;">null</span> <span style="color: #008000;">/*</span><span style="color: #008000;">inTask</span><span style="color: #008000;">*/</span>, "startHomeActivity: " +<span style="color: #000000;"> reason);
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (mSupervisor.inResumeTopActivity) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If we are in resume section already, home activity will be initialized, but not
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> resumed (to avoid recursive resume) and will stay that way until something pokes it
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> again. We need to schedule another resume.</span>
<span style="color: #000000;">            mSupervisor.scheduleResumeTopActivities();
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp; 后面就进入了Activity的启动流程了。</p>
</div>