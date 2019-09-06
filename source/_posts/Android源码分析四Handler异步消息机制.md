---
title: Android 源码分析（四） Handler 异步消息机制
date: 2018-07-07 12:40:19
tags: 安卓源码
categories: 
- Android
- 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/handler.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.Handler 使用方法:</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre> Handler handler = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Handler() {
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> handleMessage(Message msg) {
            </span><span style="color: #0000ff;">super</span><span style="color: #000000;">.handleMessage(msg);
            </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (msg.what) {
                </span><span style="color: #0000ff;">case</span> 1<span style="color: #000000;">:
                    Log.i(TAG,</span>"UI thread sendEmptyMessage...1"<span style="color: #000000;">);
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">case</span> 2<span style="color: #000000;">:
                    Log.i(TAG,</span>"thread sendEmptyMessage...2"<span style="color: #000000;">);
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">case</span> 3<span style="color: #000000;">:
                    Log.i(TAG,</span>"thread sendMessage...3"<span style="color: #000000;">);
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">case</span> 4<span style="color: #000000;">:
                    Log.i(TAG,</span>"UI thread sendMessage...4"<span style="color: #000000;">);
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            }
        }
    };
    
    
    handler.sendEmptyMessage(</span>1<span style="color: #000000;">);

    handler.postDelayed(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> Runnable() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> run() {
                handler.sendEmptyMessage(</span>2<span style="color: #000000;">);

                Message msg </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Message();
                msg.what </span>= 3<span style="color: #000000;">;
                handler.sendMessage(msg);
            }
        }, </span>1000<span style="color: #000000;">);<br>
    Message msg2 </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Message();
    msg2.what </span>= 4<span style="color: #000000;">;
    handler.sendMessage(msg2);</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>输出结果:</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180707123135364-1224869612.png" alt=""></p>
<h2>二.源码分析</h2>
<pre><span style="color: #000000;">从handler.sendMessage(msg)进去看看源码怎么调用的.<br></span></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>//<span style="color: #000000;">Handler.java
        
        </span><span style="color: #008000;">//</span><span style="color: #008000;">Looper.prepare()方法初始化了一个Looper对象并关联在一个MessageQueue对象，并且一个线程中只有一个Looper对象，只有一个MessageQueue对象。
        </span><span style="color: #008000;">//</span><span style="color: #008000;">Handler的构造方法则在Handler内部维护了当前线程的Looper对象</span>
    <span style="color: #0000ff;">public</span> Handler(Callback callback, <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> async) {
        ......
        mLooper </span>=<span style="color: #000000;"> Looper.myLooper();
        </span><span style="color: #0000ff;">if</span> (mLooper == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(
                </span>"Can't create handler inside thread that has not called Looper.prepare()"<span style="color: #000000;">);
        }
        ......
    }
        
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> if we can get rid of this method, the handler need not remember its loop
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> we could instead export a getMessageQueue() method... </span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">final</span><span style="color: #000000;"> Looper getLooper() {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mLooper;
    }

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Pushes a message onto the end of the message queue after all pending messages
     * before the current time. It will be received in {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #handleMessage},
     * in the thread attached to this handler.
     *  
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> Returns true if the message was successfully placed in to the 
     *         message queue.  Returns false on failure, usually because the
     *         looper processing the message queue is exiting.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> sendMessage(Message msg)
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;">参数 0 代表,无延迟.</span>
        <span style="color: #0000ff;">return</span> sendMessageDelayed(msg, 0<span style="color: #000000;">);
    }
    
     </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Enqueue a message into the message queue after all pending messages
     * before (current time + delayMillis). You will receive it in
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #handleMessage}, in the thread attached to this handler.
     *  
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> Returns true if the message was successfully placed in to the 
     *         message queue.  Returns false on failure, usually because the
     *         looper processing the message queue is exiting.  Note that a
     *         result of true does not mean the message will be processed -- if
     *         the looper is quit before the delivery time of the message
     *         occurs then the message will be dropped.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">boolean</span> sendMessageDelayed(Message msg, <span style="color: #0000ff;">long</span><span style="color: #000000;"> delayMillis)
    {
        </span><span style="color: #0000ff;">if</span> (delayMillis &lt; 0<span style="color: #000000;">) {
            delayMillis </span>= 0<span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">return</span> sendMessageAtTime(msg, SystemClock.uptimeMillis() +<span style="color: #000000;"> delayMillis);
    }
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Enqueue a message into the message queue after all pending messages
     * before the absolute time (in milliseconds) &lt;var&gt;uptimeMillis&lt;/var&gt;.
     * &lt;b&gt;The time-base is {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.os.SystemClock#uptimeMillis}.&lt;/b&gt;
     * Time spent in deep sleep will add an additional delay to execution.
     * You will receive it in {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #handleMessage}, in the thread attached
     * to this handler.
     * 
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> uptimeMillis The absolute time at which the message should be
     *         delivered, using the
     *         {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.os.SystemClock#uptimeMillis} time-base.
     *         
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> Returns true if the message was successfully placed in to the 
     *         message queue.  Returns false on failure, usually because the
     *         looper processing the message queue is exiting.  Note that a
     *         result of true does not mean the message will be processed -- if
     *         the looper is quit before the delivery time of the message
     *         occurs then the message will be dropped.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span> sendMessageAtTime(Message msg, <span style="color: #0000ff;">long</span><span style="color: #000000;"> uptimeMillis) {
        MessageQueue queue </span>=<span style="color: #000000;"> mQueue;
        </span><span style="color: #0000ff;">if</span> (queue == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            RuntimeException e </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> RuntimeException(
                    </span><span style="color: #0000ff;">this</span> + " sendMessageAtTime() called with no mQueue"<span style="color: #000000;">);
            Log.w(</span>"Looper"<span style="color: #000000;">, e.getMessage(), e);
            </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> enqueueMessage(queue, msg, uptimeMillis);
    }
    
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">boolean</span> enqueueMessage(MessageQueue queue, Message msg, <span style="color: #0000ff;">long</span><span style="color: #000000;"> uptimeMillis) {
        msg.target </span>= <span style="color: #0000ff;">this</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (mAsynchronous) {
            msg.setAsynchronous(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> queue.enqueueMessage(msg, uptimeMillis);
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;">msg.target就是Handler对象本身；而这里的queue对象就是我们的Handler内部维护的Looper对象关联的MessageQueue对象.</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<pre></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">MessageQueue.java</span>
    <span style="color: #0000ff;">boolean</span> enqueueMessage(Message msg, <span style="color: #0000ff;">long</span><span style="color: #000000;"> when) {
        </span><span style="color: #0000ff;">if</span> (msg.target == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalArgumentException("Message must have a target."<span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (msg.isInUse()) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalStateException(msg + " This message is already in use."<span style="color: #000000;">);
        }

        </span><span style="color: #0000ff;">synchronized</span> (<span style="color: #0000ff;">this</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (mQuitting) {
                IllegalStateException e </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> IllegalStateException(
                        msg.target </span>+ " sending message to a Handler on a dead thread"<span style="color: #000000;">);
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">;
            }

            msg.markInUse();
            msg.when </span>=<span style="color: #000000;"> when;
            Message p </span>=<span style="color: #000000;"> mMessages;
            </span><span style="color: #0000ff;">boolean</span><span style="color: #000000;"> needWake;
            </span><span style="color: #0000ff;">if</span> (p == <span style="color: #0000ff;">null</span> || when == 0 || when &lt;<span style="color: #000000;"> p.when) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> New head, wake up the event queue if blocked.</span>
                msg.next =<span style="color: #000000;"> p;
                mMessages </span>=<span style="color: #000000;"> msg;
                needWake </span>=<span style="color: #000000;"> mBlocked;
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Inserted within the middle of the queue.  Usually we don't have to wake
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> up the event queue unless there is a barrier at the head of the queue
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> and the message is the earliest asynchronous message in the queue.</span>
                needWake = mBlocked &amp;&amp; p.target == <span style="color: #0000ff;">null</span> &amp;&amp;<span style="color: #000000;"> msg.isAsynchronous();
                Message prev;
                </span><span style="color: #0000ff;">for</span><span style="color: #000000;"> (;;) {
                    prev </span>=<span style="color: #000000;"> p;
                    p </span>=<span style="color: #000000;"> p.next;
                    </span><span style="color: #0000ff;">if</span> (p == <span style="color: #0000ff;">null</span> || when &lt;<span style="color: #000000;"> p.when) {
                        </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                    }
                    </span><span style="color: #0000ff;">if</span> (needWake &amp;&amp;<span style="color: #000000;"> p.isAsynchronous()) {
                        needWake </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
                    }
                }
                msg.next </span>= p; <span style="color: #008000;">//</span><span style="color: #008000;"> invariant: p == prev.next</span>
                prev.next =<span style="color: #000000;"> msg;
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> We can assume mPtr != 0 because mQuitting is false.</span>
            <span style="color: #0000ff;">if</span><span style="color: #000000;"> (needWake) {
                nativeWake(mPtr);
            }
        }
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">Looper.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Run the message queue in this thread. Be sure to call
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #quit()} to end the loop.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> loop() {
        </span><span style="color: #0000ff;">final</span> Looper me =<span style="color: #000000;"> myLooper();
        </span><span style="color: #0000ff;">if</span> (me == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("No Looper; Looper.prepare() wasn't called on this thread."<span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">final</span> MessageQueue queue =<span style="color: #000000;"> me.mQueue;

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make sure the identity of this thread is that of the local process,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> and keep track of what that identity token actually is.</span>
<span style="color: #000000;">        Binder.clearCallingIdentity();
        </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> ident =<span style="color: #000000;"> Binder.clearCallingIdentity();

        </span><span style="color: #008000;">//</span><span style="color: #008000;">Looper.loop()方法里起了一个死循环，不断的判断MessageQueue中的消息是否为空，如果为空则直接return掉，然后执行queue.next()方法：</span>
        <span style="color: #0000ff;">for</span><span style="color: #000000;"> (;;) {
            Message msg </span>= queue.next(); <span style="color: #008000;">//</span><span style="color: #008000;"> might block</span>
            <span style="color: #0000ff;">if</span> (msg == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> No message indicates that the message queue is quitting.</span>
                <span style="color: #0000ff;">return</span><span style="color: #000000;">;
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> This must be in a local variable, in case a UI event sets the logger</span>
            <span style="color: #0000ff;">final</span> Printer logging =<span style="color: #000000;"> me.mLogging;
            </span><span style="color: #0000ff;">if</span> (logging != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                logging.println(</span>"&gt;&gt;&gt;&gt;&gt; Dispatching to " + msg.target + " " +<span style="color: #000000;">
                        msg.callback </span>+ ": " +<span style="color: #000000;"> msg.what);
            }

            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> slowDispatchThresholdMs =<span style="color: #000000;"> me.mSlowDispatchThresholdMs;

            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> traceTag =<span style="color: #000000;"> me.mTraceTag;
            </span><span style="color: #0000ff;">if</span> (traceTag != 0 &amp;&amp;<span style="color: #000000;"> Trace.isTagEnabled(traceTag)) {
                Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
            }
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> start = (slowDispatchThresholdMs == 0) ? 0<span style="color: #000000;"> : SystemClock.uptimeMillis();
            </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span><span style="color: #000000;"> end;
            </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">msg.target就是Handler对象本身</span>
<span style="color: #000000;">                msg.target.dispatchMessage(msg);
                end </span>= (slowDispatchThresholdMs == 0) ? 0<span style="color: #000000;"> : SystemClock.uptimeMillis();
            } </span><span style="color: #0000ff;">finally</span><span style="color: #000000;"> {
                </span><span style="color: #0000ff;">if</span> (traceTag != 0<span style="color: #000000;">) {
                    Trace.traceEnd(traceTag);
                }
            }
            </span><span style="color: #0000ff;">if</span> (slowDispatchThresholdMs &gt; 0<span style="color: #000000;">) {
                </span><span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> time = end -<span style="color: #000000;"> start;
                </span><span style="color: #0000ff;">if</span> (time &gt;<span style="color: #000000;"> slowDispatchThresholdMs) {
                    Slog.w(TAG, </span>"Dispatch took " + time + "ms on "
                            + Thread.currentThread().getName() + ", h=" +<span style="color: #000000;">
                            msg.target </span>+ " cb=" + msg.callback + " msg=" +<span style="color: #000000;"> msg.what);
                }
            }

            </span><span style="color: #0000ff;">if</span> (logging != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                logging.println(</span>"&lt;&lt;&lt;&lt;&lt; Finished to " + msg.target + " " +<span style="color: #000000;"> msg.callback);
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make sure that during the course of dispatching the
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> identity of the thread wasn't corrupted.</span>
            <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">long</span> newIdent =<span style="color: #000000;"> Binder.clearCallingIdentity();
            </span><span style="color: #0000ff;">if</span> (ident !=<span style="color: #000000;"> newIdent) {
                Log.wtf(TAG, </span>"Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" +<span style="color: #000000;"> msg.what);
            }

            msg.recycleUnchecked();
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>又回到Handler.java</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">Handle.java</span>
    <span style="color: #008000;">/**</span><span style="color: #008000;">
     * Handle system messages here.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> dispatchMessage(Message msg) {
        </span><span style="color: #0000ff;">if</span> (msg.callback != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            handleCallback(msg);
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">if</span> (mCallback != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (mCallback.handleMessage(msg)) {
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
                }
            }
            handleMessage(msg);
        }
    }

    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> handleCallback(Message message) {
        message.callback.run();
    }
    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>补充: UI线程 调用 Loop.</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #008000;">//</span><span style="color: #008000;">ActivityThread.java</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> main(String[] args) {
        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, </span>"ActivityThreadMain"<span style="color: #000000;">);
        SamplingProfilerIntegration.start();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> CloseGuard defaults to true and can be quite spammy.  We
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> disable it here, but selectively enable it later (via
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> StrictMode) on debug builds, but using DropBox, not logs.</span>
        CloseGuard.setEnabled(<span style="color: #0000ff;">false</span><span style="color: #000000;">);

        Environment.initForCurrentUser();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set the reporter for event logging in libcore</span>
        EventLogger.setReporter(<span style="color: #0000ff;">new</span><span style="color: #000000;"> EventLoggingReporter());

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make sure TrustedCertificateStore looks in the right place for CA certificates</span>
        <span style="color: #0000ff;">final</span> File configDir =<span style="color: #000000;"> Environment.getUserConfigDirectory(UserHandle.myUserId());
        TrustedCertificateStore.setDefaultUserDirectory(configDir);

        Process.setArgV0(</span>"&lt;pre-initialized&gt;"<span style="color: #000000;">);

        Looper.prepareMainLooper();

        ActivityThread thread </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ActivityThread();
        thread.attach(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);

        </span><span style="color: #0000ff;">if</span> (sMainThreadHandler == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            sMainThreadHandler </span>=<span style="color: #000000;"> thread.getHandler();
        }

        </span><span style="color: #0000ff;">if</span> (<span style="color: #0000ff;">false</span><span style="color: #000000;">) {
            Looper.myLooper().setMessageLogging(</span><span style="color: #0000ff;">new</span><span style="color: #000000;">
                    LogPrinter(Log.DEBUG, </span>"ActivityThread"<span style="color: #000000;">));
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> End of event ActivityThreadMain.</span>
<span style="color: #000000;">        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">主线程启动就调用了loop().</span>
<span style="color: #000000;">        Looper.loop();

        </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> RuntimeException("Main thread loop unexpectedly exited"<span style="color: #000000;">);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180707123851869-829464379.png" alt=""></h2>
<h2>三.总结:</h2>
<p>一.Handler,Looper,MessageQue三者关系&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; &nbsp;</p>
<p>　　1.Looper：相当于消息的载体<br><br>&nbsp;&nbsp;&nbsp;&nbsp; 　　1) 它的内部有一个消息队列，也就是MessageQueue，Handler发送的所有消息都会走向这个消息队里。<br><br>&nbsp;&nbsp;&nbsp;&nbsp; 　　2) 它的Looper.loop方法是一个死循环，不断的从消息队列MessageQueue中取出消息。如果有消息存在就处理该消息，否则就阻塞。<br><br>&nbsp;&nbsp; &nbsp;2.MessageQue：就是一个消息队列，可以向其中添加消息并处理消息。<br><br>&nbsp;&nbsp; &nbsp;3.Handler其实就是发送消息处理消息的封装。它与Looper相关联，也就是说在Handler的内部可以找到Looper，找到了Looper就找到了相应的消息队列。因此Handler发送的消息都会走向MessageQueue。<br>&nbsp;&nbsp; &nbsp;&nbsp;&nbsp; </p>
<p>&nbsp;二.为什么不能在子线程中更新UI&nbsp;&nbsp; &nbsp;</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 如果在子线程更新UI，那么就会出现这样子的一种况：其中一个子线程更新界面还没有完成，另外一个子线程就又去更新UI了，这样子会造成子UI界面错乱。那么你可能会说，我们可以实行加锁机制啊，让更新UI的代码不能并发执行。如果每一个子线程都加锁，那么 毫无疑问程序的性能将会大大下降。因此主要的原因总结起来就是两点：<br><br>&nbsp;&nbsp; &nbsp;（1）为了防止界面错乱<br><br>&nbsp;&nbsp; &nbsp;（2）为了防止程序性能下降<br><br>&nbsp;&nbsp;&nbsp;&nbsp; 因此android不允许在子线程中更新UI。为了解决这个这个问题，android就设计出这样子的一套消息处理机制，让我们尽管在子线程中发送更新UI的消息，而不用去关心多线程问题。在主线程的消息队里中采取轮询更新处理。</p>
<pre><span style="color: #000000;">&nbsp;</span></pre>
</div>