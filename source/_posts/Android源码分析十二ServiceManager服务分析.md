---
title: Android源码分析（十二）ServiceManager服务分析
date: 2018-07-22 11:44:46
tags: 安卓源码
categories: 
- Android
- 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/ServiceManager.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.启动过程分析</h2>
<div>基于 binder 机制实现通信，添加服务，查询服务，获取服务。查询，获取服务时候需要检查权限，android是基于Linux底层，所以也很好的实现了linux多用户管理。</div>
<div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">frameworks\native\cmds\servicemanager\servicemanager.rc
service servicemanager </span>/system/bin/<span style="color: #000000;">servicemanager
    </span><span style="color: #0000ff;">class</span><span style="color: #000000;"> core animation
    user system
    group system readproc
    critical
    onrestart restart healthd
    onrestart restart zygote
    onrestart restart audioserver
    onrestart restart media
    onrestart restart surfaceflinger
    onrestart restart inputflinger
    onrestart restart drm
    onrestart restart cameraserver
    writepid </span>/dev/cpuset/system-background/tasks</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>从上面可知，如果ServiceManager服务异常退出的话，系统会重启。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">frameworks\native\cmds\servicemanager\service_manager.c
</span><span style="color: #0000ff;">int</span> main(<span style="color: #0000ff;">int</span> argc, <span style="color: #0000ff;">char</span>**<span style="color: #000000;"> argv)
{
    </span><span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs;
    union selinux_callback cb;
    </span><span style="color: #0000ff;">char</span> *<span style="color: #000000;">driver;

    </span><span style="color: #0000ff;">if</span> (argc &gt; <span style="color: #800080;">1</span><span style="color: #000000;">) {
        driver </span>= argv[<span style="color: #800080;">1</span><span style="color: #000000;">];
    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
        driver </span>= <span style="color: #800000;">"</span><span style="color: #800000;">/dev/binder</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    }

    bs </span>= binder_open(driver, <span style="color: #800080;">128</span>*<span style="color: #800080;">1024</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">bs) {
    #ifdef VENDORSERVICEMANAGER
        ALOGW(</span><span style="color: #800000;">"</span><span style="color: #800000;">failed to open binder driver %s\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, driver);
        </span><span style="color: #0000ff;">while</span> (<span style="color: #0000ff;">true</span><span style="color: #000000;">) {
            sleep(UINT_MAX);
        }
    </span><span style="color: #0000ff;">#else</span><span style="color: #000000;">
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">failed to open binder driver %s\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, driver);
    </span><span style="color: #0000ff;">#endif</span>
        <span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (binder_become_context_manager(bs)) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">cannot become context manager (%s)\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, strerror(errno));
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    cb.func_audit </span>=<span style="color: #000000;"> audit_callback;
    selinux_set_callback(SELINUX_CB_AUDIT, cb);
    cb.func_log </span>=<span style="color: #000000;"> selinux_log_callback;
    selinux_set_callback(SELINUX_CB_LOG, cb);

    #ifdef VENDORSERVICEMANAGER
    sehandle </span>=<span style="color: #000000;"> selinux_android_vendor_service_context_handle();
    </span><span style="color: #0000ff;">#else</span><span style="color: #000000;">
    sehandle </span>=<span style="color: #000000;"> selinux_android_service_context_handle();
    </span><span style="color: #0000ff;">#endif</span><span style="color: #000000;">
    selinux_status_open(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);

    </span><span style="color: #0000ff;">if</span> (sehandle ==<span style="color: #000000;"> NULL) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">SELinux: Failed to acquire sehandle. Aborting.\n</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        abort();
    }

    </span><span style="color: #0000ff;">if</span> (getcon(&amp;service_manager_context) != <span style="color: #800080;">0</span><span style="color: #000000;">) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">SELinux: Failed to acquire service_manager context. Aborting.\n</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        abort();
    }


    </span><span style="color: #008000;">//</span><span style="color: #008000;">loop 接受消息。并将binder解析完的消息返回给svcmgr_handler处理。</span>
<span style="color: #000000;">    binder_loop(bs, svcmgr_handler);

    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>从这我们知道，ServiceManager是基于binder机制实现的。进入binder.c中了解下binder_open,binder_loop，然后binder将解析完的消息，返回给svcmag_handler处理</p>
<p>frameworks\native\cmds\servicemanager\binder.c</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('c271bc75-156b-40e2-ae92-90551ddf233c')"><img id="code_img_closed_c271bc75-156b-40e2-ae92-90551ddf233c" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_c271bc75-156b-40e2-ae92-90551ddf233c" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('c271bc75-156b-40e2-ae92-90551ddf233c',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_c271bc75-156b-40e2-ae92-90551ddf233c" class="cnblogs_code_hide">
<pre>    <span style="color: #0000ff;">struct</span> binder_state *binder_open(<span style="color: #0000ff;">const</span> <span style="color: #0000ff;">char</span>*<span style="color: #000000;"> driver, size_t mapsize)
{
    </span><span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs;
    </span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> binder_version vers;

    bs </span>= malloc(<span style="color: #0000ff;">sizeof</span>(*<span style="color: #000000;">bs));
    </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">bs) {
        errno </span>=<span style="color: #000000;"> ENOMEM;
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> NULL;
    }

    bs</span>-&gt;fd = open(driver, O_RDWR |<span style="color: #000000;"> O_CLOEXEC);
    </span><span style="color: #0000ff;">if</span> (bs-&gt;fd &lt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
        fprintf(stderr,</span><span style="color: #800000;">"</span><span style="color: #800000;">binder: cannot open %s (%s)\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                driver, strerror(errno));
        </span><span style="color: #0000ff;">goto</span><span style="color: #000000;"> fail_open;
    }

    </span><span style="color: #0000ff;">if</span> ((ioctl(bs-&gt;fd, BINDER_VERSION, &amp;vers) == -<span style="color: #800080;">1</span>) ||<span style="color: #000000;">
        (vers.protocol_version </span>!=<span style="color: #000000;"> BINDER_CURRENT_PROTOCOL_VERSION)) {
        fprintf(stderr,
                </span><span style="color: #800000;">"</span><span style="color: #800000;">binder: kernel driver version (%d) differs from user space version (%d)\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                vers.protocol_version, BINDER_CURRENT_PROTOCOL_VERSION);
        </span><span style="color: #0000ff;">goto</span><span style="color: #000000;"> fail_open;
    }

    bs</span>-&gt;mapsize =<span style="color: #000000;"> mapsize;
    bs</span>-&gt;mapped = mmap(NULL, mapsize, PROT_READ, MAP_PRIVATE, bs-&gt;fd, <span style="color: #800080;">0</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">if</span> (bs-&gt;mapped ==<span style="color: #000000;"> MAP_FAILED) {
        fprintf(stderr,</span><span style="color: #800000;">"</span><span style="color: #800000;">binder: cannot map device (%s)\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                strerror(errno));
        </span><span style="color: #0000ff;">goto</span><span style="color: #000000;"> fail_map;
    }

    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> bs;
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<div class="cnblogs_code" onclick="cnblogs_code_show('5c06b747-4566-4459-8bff-9b4c249774c5')"><img id="code_img_closed_5c06b747-4566-4459-8bff-9b4c249774c5" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_5c06b747-4566-4459-8bff-9b4c249774c5" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('5c06b747-4566-4459-8bff-9b4c249774c5',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_5c06b747-4566-4459-8bff-9b4c249774c5" class="cnblogs_code_hide">
<pre><span style="color: #0000ff;">void</span> binder_loop(<span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs, binder_handler func)
{
    </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> res;
    </span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> binder_write_read bwr;
    uint32_t readbuf[</span><span style="color: #800080;">32</span><span style="color: #000000;">];

    bwr.write_size </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    bwr.write_consumed </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    bwr.write_buffer </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;

    readbuf[</span><span style="color: #800080;">0</span>] =<span style="color: #000000;"> BC_ENTER_LOOPER;
    binder_write(bs, readbuf, </span><span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(uint32_t));

    </span><span style="color: #0000ff;">for</span><span style="color: #000000;"> (;;) {
        bwr.read_size </span>= <span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(readbuf);
        bwr.read_consumed </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
        bwr.read_buffer </span>=<span style="color: #000000;"> (uintptr_t) readbuf;

        res </span>= ioctl(bs-&gt;fd, BINDER_WRITE_READ, &amp;<span style="color: #000000;">bwr);

        </span><span style="color: #0000ff;">if</span> (res &lt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">binder_loop: ioctl failed (%s)\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, strerror(errno));
            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;">解析消息</span>
        res = binder_parse(bs, <span style="color: #800080;">0</span><span style="color: #000000;">, (uintptr_t) readbuf, bwr.read_consumed, func);
        </span><span style="color: #0000ff;">if</span> (res == <span style="color: #800080;">0</span><span style="color: #000000;">) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">binder_loop: unexpected reply?!\n</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">if</span> (res &lt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">binder_loop: io error %d %s\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, res, strerror(errno));
            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
        }
    }
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<div>binder_parse 解析接受到的消息&nbsp;</div>
<div>binder_send_reply 接收到消息，并解析完消息后，binder将解析后的消息返回给ServiceManager</div>
<div>
<div class="cnblogs_code" onclick="cnblogs_code_show('4aebc7de-d31e-48f8-9dbc-8ae2582c5d95')"><img id="code_img_closed_4aebc7de-d31e-48f8-9dbc-8ae2582c5d95" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_4aebc7de-d31e-48f8-9dbc-8ae2582c5d95" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('4aebc7de-d31e-48f8-9dbc-8ae2582c5d95',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_4aebc7de-d31e-48f8-9dbc-8ae2582c5d95" class="cnblogs_code_hide">
<pre><span style="color: #0000ff;">void</span> binder_send_reply(<span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs,
                       </span><span style="color: #0000ff;">struct</span> binder_io *<span style="color: #000000;">reply,
                       binder_uintptr_t buffer_to_free,
                       </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> status)
{
    </span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> {
        uint32_t cmd_free;
        binder_uintptr_t buffer;
        uint32_t cmd_reply;
        </span><span style="color: #0000ff;">struct</span><span style="color: #000000;"> binder_transaction_data txn;
    } __attribute__((packed)) data;

    data.cmd_free </span>=<span style="color: #000000;"> BC_FREE_BUFFER;
    data.buffer </span>=<span style="color: #000000;"> buffer_to_free;
    data.cmd_reply </span>=<span style="color: #000000;"> BC_REPLY;
    data.txn.target.ptr </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    data.txn.cookie </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    data.txn.code </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (status) {
        data.txn.flags </span>=<span style="color: #000000;"> TF_STATUS_CODE;
        data.txn.data_size </span>= <span style="color: #0000ff;">sizeof</span>(<span style="color: #0000ff;">int</span><span style="color: #000000;">);
        data.txn.offsets_size </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
        data.txn.data.ptr.buffer </span>= (uintptr_t)&amp;<span style="color: #000000;">status;
        data.txn.data.ptr.offsets </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
        data.txn.flags </span>= <span style="color: #800080;">0</span><span style="color: #000000;">;
        data.txn.data_size </span>= reply-&gt;data - reply-&gt;<span style="color: #000000;">data0;
        data.txn.offsets_size </span>= ((<span style="color: #0000ff;">char</span>*) reply-&gt;offs) - ((<span style="color: #0000ff;">char</span>*) reply-&gt;<span style="color: #000000;">offs0);
        data.txn.data.ptr.buffer </span>= (uintptr_t)reply-&gt;<span style="color: #000000;">data0;
        data.txn.data.ptr.offsets </span>= (uintptr_t)reply-&gt;<span style="color: #000000;">offs0;
    }
    binder_write(bs, </span>&amp;data, <span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(data));
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>继续回到serviceManager里分析binder解析返回回来的服务消息</p>
<p>frameworks\native\cmds\servicemanager\servicemanager.rc</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">int</span> svcmgr_handler(<span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs,
                   </span><span style="color: #0000ff;">struct</span> binder_transaction_data *<span style="color: #000000;">txn,
                   </span><span style="color: #0000ff;">struct</span> binder_io *<span style="color: #000000;">msg,
                   </span><span style="color: #0000ff;">struct</span> binder_io *<span style="color: #000000;">reply)
{
    </span><span style="color: #0000ff;">struct</span> svcinfo *<span style="color: #000000;">si;
    uint16_t </span>*<span style="color: #000000;">s;
    size_t len;
    uint32_t handle;
    uint32_t strict_policy;
    </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> allow_isolated;

    </span><span style="color: #008000;">//</span><span style="color: #008000;">ALOGI("target=%p code=%d pid=%d uid=%d\n",
    </span><span style="color: #008000;">//</span><span style="color: #008000;">      (void*) txn-&gt;target.ptr, txn-&gt;code, txn-&gt;sender_pid, txn-&gt;sender_euid);</span>

    <span style="color: #0000ff;">if</span> (txn-&gt;target.ptr !=<span style="color: #000000;"> BINDER_SERVICE_MANAGER)
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;

    </span><span style="color: #0000ff;">if</span> (txn-&gt;code ==<span style="color: #000000;"> PING_TRANSACTION)
        </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Equivalent to Parcel::enforceInterface(), reading the RPC
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> header with the strict mode policy mask and the interface name.
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Note that we ignore the strict_policy and don't propagate it
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> further (since we do no outbound RPCs anyway).</span>
    strict_policy =<span style="color: #000000;"> bio_get_uint32(msg);
    s </span>= bio_get_string16(msg, &amp;<span style="color: #000000;">len);
    </span><span style="color: #0000ff;">if</span> (s ==<span style="color: #000000;"> NULL) {
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;">检查是否是servicemanager服务</span>
    <span style="color: #0000ff;">if</span> ((len != (<span style="color: #0000ff;">sizeof</span>(svcmgr_id) / <span style="color: #800080;">2</span>)) ||<span style="color: #000000;">
        memcmp(svcmgr_id, s, </span><span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(svcmgr_id))) {
        fprintf(stderr,</span><span style="color: #800000;">"</span><span style="color: #800000;">invalid id %s\n</span><span style="color: #800000;">"</span><span style="color: #000000;">, str8(s, len));
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000ff;">if</span> (sehandle &amp;&amp; selinux_status_updated() &gt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
        </span><span style="color: #0000ff;">struct</span> selabel_handle *tmp_sehandle =<span style="color: #000000;"> selinux_android_service_context_handle();
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (tmp_sehandle) {
            selabel_close(sehandle);
            sehandle </span>=<span style="color: #000000;"> tmp_sehandle;
        }
    }

    </span><span style="color: #0000ff;">switch</span>(txn-&gt;<span style="color: #000000;">code) {
    </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> SVC_MGR_GET_SERVICE:
    </span><span style="color: #008000;">//</span><span style="color: #008000;">检查服务，do_find_service 查找服务。</span>
    <span style="color: #0000ff;">case</span><span style="color: #000000;"> SVC_MGR_CHECK_SERVICE:
        s </span>= bio_get_string16(msg, &amp;<span style="color: #000000;">len);
        </span><span style="color: #0000ff;">if</span> (s ==<span style="color: #000000;"> NULL) {
            </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
        }
        handle </span>= do_find_service(s, len, txn-&gt;sender_euid, txn-&gt;<span style="color: #000000;">sender_pid);
        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">handle)
            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
        bio_put_ref(reply, handle);
        </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;

    </span><span style="color: #008000;">//</span><span style="color: #008000;">添加服务 do_add_service 获取服务 在do_add_service会检查是否具有权限</span>
    <span style="color: #0000ff;">case</span><span style="color: #000000;"> SVC_MGR_ADD_SERVICE:
        s </span>= bio_get_string16(msg, &amp;<span style="color: #000000;">len);
        </span><span style="color: #0000ff;">if</span> (s ==<span style="color: #000000;"> NULL) {
            </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
        }
        handle </span>=<span style="color: #000000;"> bio_get_ref(msg);
        allow_isolated </span>= bio_get_uint32(msg) ? <span style="color: #800080;">1</span> : <span style="color: #800080;">0</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span> (do_add_service(bs, s, len, handle, txn-&gt;<span style="color: #000000;">sender_euid,
            allow_isolated, txn</span>-&gt;<span style="color: #000000;">sender_pid))
            </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
    </span><span style="color: #008000;">//</span><span style="color: #008000;">查询服务</span>
    <span style="color: #0000ff;">case</span><span style="color: #000000;"> SVC_MGR_LIST_SERVICES: {
        uint32_t n </span>=<span style="color: #000000;"> bio_get_uint32(msg);

        </span><span style="color: #0000ff;">if</span> (!svc_can_list(txn-&gt;sender_pid, txn-&gt;<span style="color: #000000;">sender_euid)) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">list_service() uid=%d - PERMISSION DENIED\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                    txn</span>-&gt;<span style="color: #000000;">sender_euid);
            </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
        }
        si </span>=<span style="color: #000000;"> svclist;
        </span><span style="color: #0000ff;">while</span> ((n-- &gt; <span style="color: #800080;">0</span>) &amp;&amp;<span style="color: #000000;"> si)
            si </span>= si-&gt;<span style="color: #000000;">next;
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (si) {
            bio_put_string16(reply, si</span>-&gt;<span style="color: #000000;">name);
            </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
        }
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }
    </span><span style="color: #0000ff;">default</span><span style="color: #000000;">:
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">unknown code %d\n</span><span style="color: #800000;">"</span>, txn-&gt;<span style="color: #000000;">code);
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    bio_put_uint32(reply, </span><span style="color: #800080;">0</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>进入 do_find_service&nbsp; do_add_service了解下做了什么事。</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('259cf340-561d-4ea7-9910-93cac3ea1148')"><img id="code_img_closed_259cf340-561d-4ea7-9910-93cac3ea1148" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_259cf340-561d-4ea7-9910-93cac3ea1148" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('259cf340-561d-4ea7-9910-93cac3ea1148',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_259cf340-561d-4ea7-9910-93cac3ea1148" class="cnblogs_code_hide">
<pre>uint32_t do_find_service(<span style="color: #0000ff;">const</span> uint16_t *<span style="color: #000000;">s, size_t len, uid_t uid, pid_t spid)
{
    </span><span style="color: #0000ff;">struct</span> svcinfo *si =<span style="color: #000000;"> find_svc(s, len);

    </span><span style="color: #0000ff;">if</span> (!si || !si-&gt;<span style="color: #000000;">handle) {
        </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000ff;">if</span> (!si-&gt;<span style="color: #000000;">allow_isolated) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> If this service doesn't allow access from isolated processes,
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> then check the uid to see if it is isolated.</span>
        uid_t appid = uid %<span style="color: #000000;"> AID_USER;
        </span><span style="color: #0000ff;">if</span> (appid &gt;= AID_ISOLATED_START &amp;&amp; appid &lt;=<span style="color: #000000;"> AID_ISOLATED_END) {
            </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
        }
    }

    </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">svc_can_find(s, len, spid, uid)) {
        </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000ff;">return</span> si-&gt;<span style="color: #000000;">handle;
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<div class="cnblogs_code" onclick="cnblogs_code_show('786c6178-2ad5-466d-b516-d8e38fe7b404')"><img id="code_img_closed_786c6178-2ad5-466d-b516-d8e38fe7b404" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_786c6178-2ad5-466d-b516-d8e38fe7b404" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('786c6178-2ad5-466d-b516-d8e38fe7b404',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_786c6178-2ad5-466d-b516-d8e38fe7b404" class="cnblogs_code_hide">
<pre><span style="color: #0000ff;">int</span> do_add_service(<span style="color: #0000ff;">struct</span> binder_state *<span style="color: #000000;">bs,
                   </span><span style="color: #0000ff;">const</span> uint16_t *<span style="color: #000000;">s, size_t len,
                   uint32_t handle, uid_t uid, </span><span style="color: #0000ff;">int</span><span style="color: #000000;"> allow_isolated,
                   pid_t spid)
{
    </span><span style="color: #0000ff;">struct</span> svcinfo *<span style="color: #000000;">si;

    </span><span style="color: #008000;">//</span><span style="color: #008000;">ALOGI("add_service('%s',%x,%s) uid=%d\n", str8(s, len), handle,
    </span><span style="color: #008000;">//</span><span style="color: #008000;">        allow_isolated ? "allow_isolated" : "!allow_isolated", uid);</span>

    <span style="color: #0000ff;">if</span> (!handle || (len == <span style="color: #800080;">0</span>) || (len &gt; <span style="color: #800080;">127</span><span style="color: #000000;">))
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;

    </span><span style="color: #008000;">//</span><span style="color: #008000;">检查是否有权限</span>
    <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">svc_can_register(s, len, spid, uid)) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">add_service('%s',%x) uid=%d - PERMISSION DENIED\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
             str8(s, len), handle, uid);
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    si </span>=<span style="color: #000000;"> find_svc(s, len);
    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (si) {
        </span><span style="color: #0000ff;">if</span> (si-&gt;<span style="color: #000000;">handle) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">add_service('%s',%x) uid=%d - ALREADY REGISTERED, OVERRIDE\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                 str8(s, len), handle, uid);
            svcinfo_death(bs, si);
        }
        si</span>-&gt;handle =<span style="color: #000000;"> handle;
    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
        si </span>= malloc(<span style="color: #0000ff;">sizeof</span>(*si) + (len + <span style="color: #800080;">1</span>) * <span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(uint16_t));
        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">si) {
            ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">add_service('%s',%x) uid=%d - OUT OF MEMORY\n</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                 str8(s, len), handle, uid);
            </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
        }
        si</span>-&gt;handle =<span style="color: #000000;"> handle;
        si</span>-&gt;len =<span style="color: #000000;"> len;
        memcpy(si</span>-&gt;name, s, (len + <span style="color: #800080;">1</span>) * <span style="color: #0000ff;">sizeof</span><span style="color: #000000;">(uint16_t));
        si</span>-&gt;name[len] = <span style="color: #800000;">'</span><span style="color: #800000;">\0</span><span style="color: #800000;">'</span><span style="color: #000000;">;
        si</span>-&gt;death.func = (<span style="color: #0000ff;">void</span>*<span style="color: #000000;">) svcinfo_death;
        si</span>-&gt;death.ptr =<span style="color: #000000;"> si;
        si</span>-&gt;allow_isolated =<span style="color: #000000;"> allow_isolated;
        si</span>-&gt;next =<span style="color: #000000;"> svclist;
        svclist </span>=<span style="color: #000000;"> si;
    }

    binder_acquire(bs, handle);
    binder_link_to_death(bs, handle, </span>&amp;si-&gt;<span style="color: #000000;">death);
    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<h2>二. ServiceManager如何管理服务</h2>
<p>&nbsp;从上面源码分析我们知道了ServiceManager利用binder通信机制来管理一系列服务。<br>&nbsp;上面源码的执行路径可以用下图所示：（注意，图中do_find_service7 应该是do_find_service.操作手误。）</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180722114236180-75099312.png" alt=""></p>
<p>&nbsp;</p>
<p>　　代码分析抽象成逻辑分析，如下图所示：</p>
<p>　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180722114323395-1692794298.png" alt=""></p>

</div>

</div>
</div>