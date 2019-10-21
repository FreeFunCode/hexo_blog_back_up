---
title: Android 源码分析（十） Dalvik 虚拟机创建过程
date: 2018-07-09 21:04:17
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/dalvik.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一. 介绍Dalvik</h2>
<p>　　1.java的运行需要JVM,同样android中使用了java语言，也需要一个VM。针对手机处理器和内存等硬件资源不足而推出来的一款VM，为android运行提供环境，叫DVM。</p>
<p>　　2.Dalvik虚拟机允许多个instance的存在。实际上android中的每一个app都是运行在自己VM实例之中(沙盒)。每一个VM实例在linux中又是一个单独的进程，所以可以认为是同一个概念。运行在自己的DVM进程之中，不同的app不会相互干扰，且不会因为一个DVM的崩溃导致所有的app进程都崩溃。这点来说，<strong>Android dvm的进程和Linux的进程, 应用程序的进程 概念类似。</strong></p>
<p><strong>　　3.与<span style="color: #333300;"><strong><span style="font-family: Microsoft YaHei;">JVM的区别：</span></strong></span></strong></p>
<p><strong><span style="color: #333300;"><strong><span style="font-family: Microsoft YaHei;">　　　　</span></strong></span></strong><span style="color: #333300;"><span style="font-family: Microsoft YaHei;">1.基于架构的不同。JVM是基于栈的架构，而DVM是基于寄存器架构。</span></span></p>
<p><span style="color: #333300;"><span style="font-family: Microsoft YaHei;">　　　　2.jvm运行的是字节码文件，而dvm运行自己定义的dex文件格式。</span></span></p>
<p><span style="color: #333300;"><span style="font-family: Microsoft YaHei;">　　　　　　<span style="font-size: small;">JVM编译过程 java-&gt;class-&gt;jar</span><br><span style="font-size: small;">　　　　　　 DVM编译过程java-&gt;class-&gt;dex</span></span></span></p>
<p><span style="color: #333300;"><span style="font-family: Microsoft YaHei;"><span style="font-size: small;">　　　　总结dvm与jvm区别：</span></span></span><span style="color: #333300;"><span style="font-family: Microsoft YaHei;"><span style="font-size: small;">　　　　　</span></span></span></p>
<p>　　　　区别一：dvm执行的是.dex格式文件&nbsp; jvm执行的是.class文件&nbsp;&nbsp; android程序编译完之后生产.class文件，然后，dex工具会把.class文件处理成.dex文件，然后把资源文件和.dex文件等打包成.apk文件。apk就是android package的意思。 jvm执行的是.class文件。</p>
<p>&nbsp;</p>
<p>　　　　区别二:dvm是基于寄存器的虚拟机&nbsp; 而jvm执行是基于虚拟栈的虚拟机。寄存器存取速度比栈快的多，dvm可以根据硬件实现最大的优化，比较适合移动设备。</p>
<p>&nbsp;</p>
<p>　　　　区别三：.class文件存在很多的冗余信息，dex工具会去除冗余信息，并把所有的.class文件整合到.dex文件中。减少了I/O操作，提高了类的查找速度</p>
<p>　　　　</p>
<p>　　　　一张图了解dvm主要做的事：</p>
<p>　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180709210031356-2049035324.png" alt=""></p>
<p>　　　　参考：&nbsp;https://blog.csdn.net/u010355144/article/details/47682797</p>
<h2>二.Dalvik启动过程</h2>
<p>　　</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">AndroidRuntime.cpp</span>
<span style="color: #0000ff;">int</span> AndroidRuntime::startVm(JavaVM** pJavaVM, JNIEnv** pEnv, <span style="color: #0000ff;">bool</span><span style="color: #000000;"> zygote)
{
    JavaVMInitArgs initArgs;
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> propBuf[PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> stackTraceFileBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xstacktracefile:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jniOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjniopts:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heapstartsizeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xms</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heapsizeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xmx</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heapgrowthlimitOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-XX:HeapGrowthLimit=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heapminfreeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-XX:HeapMinFree=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heapmaxfreeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-XX:HeapMaxFree=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> usejitOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xusejit:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jitmaxsizeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjitmaxsize:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jitinitialsizeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjitinitialsize:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jitthresholdOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjitthreshold:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> useJitProfilesOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjitsaveprofilinginfo:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jitprithreadweightOptBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjitprithreadweight:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> jittransitionweightOptBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xjittransitionweight:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> gctypeOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xgc:</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> backgroundgcOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-XX:BackgroundGC=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> heaptargetutilizationOptsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-XX:HeapTargetUtilization=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> cachePruneBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xzygote-max-boot-retry=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatXmsImageFlagsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xms</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatXmxImageFlagsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xmx</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatXmsFlagsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xms</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatXmxFlagsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-Xmx</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatCompilerFilterBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">--compiler-filter=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatImageCompilerFilterBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">--compiler-filter=</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatThreadsBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-j</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oatThreadsImageBuf[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">-j</span><span style="color: #800000;">"</span>)-<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> dex2oat_isa_variant_key[PROPERTY_KEY_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oat_isa_variant[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">--instruction-set-variant=</span><span style="color: #800000;">"</span>) -<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> dex2oat_isa_features_key[PROPERTY_KEY_MAX];
    </span><span style="color: #0000ff;">char</span> dex2oat_isa_features[<span style="color: #0000ff;">sizeof</span>(<span style="color: #800000;">"</span><span style="color: #800000;">--instruction-set-features=</span><span style="color: #800000;">"</span>) -<span style="color: #800080;">1</span> +<span style="color: #000000;"> PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> dex2oatFlagsBuf[PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> dex2oatImageFlagsBuf[PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> extraOptsBuf[PROPERTY_VALUE_MAX];
    </span><span style="color: #0000ff;">char</span><span style="color: #000000;"> voldDecryptBuf[PROPERTY_VALUE_MAX];
    ...
    
    </span><span style="color: #008000;">/*</span><span style="color: #008000;">
     * Initialize the VM.
     *
     * The JavaVM* is essentially per-process, and the JNIEnv* is per-thread.
     * If this call succeeds, the VM is ready, and we can start issuing
     * JNI calls.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">if</span> (JNI_CreateJavaVM(pJavaVM, pEnv, &amp;initArgs) &lt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">JNI_CreateJavaVM failed\n</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span> -<span style="color: #800080;">1</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}

</span><span style="color: #0000ff;">void</span> AndroidRuntime::start(<span style="color: #0000ff;">const</span> <span style="color: #0000ff;">char</span>* className, <span style="color: #0000ff;">const</span> Vector&lt;String8&gt;&amp; options, <span style="color: #0000ff;">bool</span><span style="color: #000000;"> zygote)
{
    
    </span><span style="color: #008000;">/*</span><span style="color: #008000;"> start the virtual machine </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    JniInvocation jni_invocation;
    jni_invocation.Init(NULL);
    JNIEnv</span>*<span style="color: #000000;"> env;
    </span><span style="color: #0000ff;">if</span> (startVm(&amp;mJavaVM, &amp;env, zygote) != <span style="color: #800080;">0</span><span style="color: #000000;">) {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
    }
    onVmCreated(env);

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">
     * Register android functions.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">if</span> (startReg(env) &lt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
        ALOGE(</span><span style="color: #800000;">"</span><span style="color: #800000;">Unable to register all android natives\n</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
    }

}<br><br><span style="color: #339966;">　　　　　//Dalvik虚拟机在Zygote进程中的启动过程，这个启动过程主要就是完成了以下四个事情：</span><br><span style="color: #339966;">&nbsp; &nbsp; &nbsp; &nbsp; //1. 创建了一个Dalvik虚拟机实例；</span><br><span style="color: #339966;">&nbsp; &nbsp; &nbsp; &nbsp; //2. 加载了Java核心类及其JNI方法；</span><br><span style="color: #339966;">&nbsp; &nbsp; &nbsp; &nbsp; //3. 为主线程的设置了一个JNI环境；</span><br><span style="color: #339966;">&nbsp; &nbsp; &nbsp; &nbsp; //4. 注册了Android核心类的JNI方法。</span><br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p><strong>Zygote 启动Dalvik作用：</strong></p>
<p>　　1. Zygote进程为Android系统准备好了一个Dalvik虚拟机实例，以后Zygote进程在创建Android应用程序进程的时候，就可以将它自身的Dalvik虚拟机实例复制到新创建Android应用程序进程中去，从而加快了Android应用程序进程的启动过程。</p>
<p><br>　　2.Java核心类和Android核心类（位于dex文件中），以及它们的JNI方法（位于so文件中），都是以内存映射的方式来读取的，因此，Zygote进程在创建Android应用程序进程的时候，除了可以将自身的Dalvik虚拟机实例复制到新创建的Android应用程序进程之外，还可以与新创建的Android应用程序进程共享Java核心类和Android核心类，以及它们的JNI方法，这样就可以节省内存消耗。</p>
<p><br>　　3.Zygote进程为了加快Android应用程序进程的启动过程，牺牲了自己的启动速度，因为它需要加载大量的Java核心类，以及注册大量的Android核心类JNI方法。Dalvik虚拟机在加载Java核心类的时候，还需要对它们进行验证以及优化，这些通常都是比较耗时的。又由于Zygote进程是由init进程启动的，也就是说Zygote进程在是开机的时候进行启动的，因此，Zygote进程的牺牲是比较大的。不过毕竟我们在玩手机的时候，很少会关机，也就是很少开机，因此，牺牲Zygote进程的启动速度是值得的，换来的是Android应用程序的快速启动。</p>
</div>