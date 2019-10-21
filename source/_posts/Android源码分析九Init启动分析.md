---
title: Android 源码分析（九） Init 启动分析
date: 2018-07-08 23:05:42
tags: 安卓源码
categories: 
- Android
- 安卓源码
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/init.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.前言:&nbsp;&nbsp;&nbsp;</h2>
<p>&nbsp;&nbsp;&nbsp; init进程 –&gt; Zygote进程 –&gt; SystemServer进程 –&gt; Launcher桌面程序 -&gt; 我们的App应用</p>
<h3>&nbsp;&nbsp;&nbsp; init进程：linux的根进程，android系统是基于linux系统的，因此可以算作是整个android操作系统的第一个进程；</h3>
<p>&nbsp;&nbsp;&nbsp; Zygote进程：android系统的根进程，主要作用：可以作用Zygote进程fork出SystemServer进程和各种应用进程；</p>
<p>&nbsp;&nbsp;&nbsp; SystemService进程：主要是在这个进程中启动系统的各项服务，比如ActivityManagerService，PackageManagerService，WindowManagerService服务等等；<br>&nbsp;&nbsp; &nbsp;<br>&nbsp;&nbsp; &nbsp;Launcher桌面程序:就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序.</p>
<h2><br>二. init 进程分析</h2>
<p>&nbsp;linux的根进程,同样也是守护进程，usb连接电脑后，cmd窗口，输入：adb shell ps 命令，能看到手机系统中当前跑的所有进程，有一个init进程。<br>&nbsp;init进程主要任务：</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180708230407266-1488130940.png" alt=""></p>
<p>&nbsp;</p>
<p>源码：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">system/core/init/init.cpp
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">1.创建目录
    </span><span style="color: #008000;">//</span><span style="color: #008000;">2.将log重定向
    </span><span style="color: #008000;">//</span><span style="color: #008000;">3.初始化环境变量
    </span><span style="color: #008000;">//</span><span style="color: #008000;">4.得到硬件信息和版本
    </span><span style="color: #008000;">//</span><span style="color: #008000;">5.解析内核启动参数
    </span><span style="color: #008000;">//</span><span style="color: #008000;">6.导入默认环境变量
    </span><span style="color: #008000;">//</span><span style="color: #008000;">7.得到系统分区</span>
<span style="color: #0000ff;">int</span> main(<span style="color: #0000ff;">int</span> argc, <span style="color: #0000ff;">char</span>**<span style="color: #000000;"> argv) {
    </span><span style="color: #0000ff;">if</span> (!strcmp(basename(argv[<span style="color: #800080;">0</span>]), <span style="color: #800000;">"</span><span style="color: #800000;">ueventd</span><span style="color: #800000;">"</span><span style="color: #000000;">)) {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> ueventd_main(argc, argv);
    }

    </span><span style="color: #0000ff;">if</span> (!strcmp(basename(argv[<span style="color: #800080;">0</span>]), <span style="color: #800000;">"</span><span style="color: #800000;">watchdogd</span><span style="color: #800000;">"</span><span style="color: #000000;">)) {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> watchdogd_main(argc, argv);
    }

    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (REBOOT_BOOTLOADER_ON_PANIC) {
        install_reboot_signal_handlers();
    }

    add_environment(</span><span style="color: #800000;">"</span><span style="color: #800000;">PATH</span><span style="color: #800000;">"</span><span style="color: #000000;">, _PATH_DEFPATH);

    </span><span style="color: #0000ff;">bool</span> is_first_stage = (getenv(<span style="color: #800000;">"</span><span style="color: #800000;">INIT_SECOND_STAGE</span><span style="color: #800000;">"</span>) ==<span style="color: #000000;"> nullptr);

    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (is_first_stage) {
        boot_clock::time_point start_time </span>=<span style="color: #000000;"> boot_clock::now();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Clear the umask.</span>
        umask(<span style="color: #800080;">0</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Get the basic filesystem setup we need put together in the initramdisk
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> on / and then we'll let the rc file figure out the rest.</span>
        mount(<span style="color: #800000;">"</span><span style="color: #800000;">tmpfs</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">/dev</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">tmpfs</span><span style="color: #800000;">"</span>, MS_NOSUID, <span style="color: #800000;">"</span><span style="color: #800000;">mode=0755</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        mkdir(</span><span style="color: #800000;">"</span><span style="color: #800000;">/dev/pts</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0755</span><span style="color: #000000;">);
        mkdir(</span><span style="color: #800000;">"</span><span style="color: #800000;">/dev/socket</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0755</span><span style="color: #000000;">);
        mount(</span><span style="color: #800000;">"</span><span style="color: #800000;">devpts</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">/dev/pts</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">devpts</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0</span><span style="color: #000000;">, NULL);
        </span><span style="color: #0000ff;">#define</span> MAKE_STR(x) __STRING(x)<span style="color: #000000;">
        mount(</span><span style="color: #800000;">"</span><span style="color: #800000;">proc</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">/proc</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">proc</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0</span>, <span style="color: #800000;">"</span><span style="color: #800000;">hidepid=2,gid=</span><span style="color: #800000;">"</span><span style="color: #000000;"> MAKE_STR(AID_READPROC));
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Don't expose the raw commandline to unprivileged processes.</span>
        chmod(<span style="color: #800000;">"</span><span style="color: #800000;">/proc/cmdline</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0440</span><span style="color: #000000;">);
        gid_t groups[] </span>=<span style="color: #000000;"> { AID_READPROC };
        setgroups(arraysize(groups), groups);
        mount(</span><span style="color: #800000;">"</span><span style="color: #800000;">sysfs</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">/sys</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">sysfs</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0</span><span style="color: #000000;">, NULL);
        mount(</span><span style="color: #800000;">"</span><span style="color: #800000;">selinuxfs</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">/sys/fs/selinux</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">selinuxfs</span><span style="color: #800000;">"</span>, <span style="color: #800080;">0</span><span style="color: #000000;">, NULL);
        mknod(</span><span style="color: #800000;">"</span><span style="color: #800000;">/dev/kmsg</span><span style="color: #800000;">"</span>, S_IFCHR | <span style="color: #800080;">0600</span>, makedev(<span style="color: #800080;">1</span>, <span style="color: #800080;">11</span><span style="color: #000000;">));
        mknod(</span><span style="color: #800000;">"</span><span style="color: #800000;">/dev/random</span><span style="color: #800000;">"</span>, S_IFCHR | <span style="color: #800080;">0666</span>, makedev(<span style="color: #800080;">1</span>, <span style="color: #800080;">8</span><span style="color: #000000;">));
        mknod(</span><span style="color: #800000;">"</span><span style="color: #800000;">/dev/urandom</span><span style="color: #800000;">"</span>, S_IFCHR | <span style="color: #800080;">0666</span>, makedev(<span style="color: #800080;">1</span>, <span style="color: #800080;">9</span><span style="color: #000000;">));

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Now that tmpfs is mounted on /dev and we have /dev/kmsg, we can actually
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> talk to the outside world...</span>
<span style="color: #000000;">        InitKernelLogging(argv);

        LOG(INFO) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">init first stage started!</span><span style="color: #800000;">"</span><span style="color: #000000;">;

        </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">DoFirstStageMount()) {
            LOG(ERROR) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">Failed to mount required partitions early ...</span><span style="color: #800000;">"</span><span style="color: #000000;">;
            panic();
        }

        SetInitAvbVersionInRecovery();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up SELinux, loading the SELinux policy.</span>
        selinux_initialize(<span style="color: #0000ff;">true</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> We're in the kernel domain, so re-exec init to transition to the init domain now
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> that the SELinux policy has been loaded.</span>
        <span style="color: #0000ff;">if</span> (restorecon(<span style="color: #800000;">"</span><span style="color: #800000;">/init</span><span style="color: #800000;">"</span>) == -<span style="color: #800080;">1</span><span style="color: #000000;">) {
            PLOG(ERROR) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">restorecon failed</span><span style="color: #800000;">"</span><span style="color: #000000;">;
            security_failure();
        }

        setenv(</span><span style="color: #800000;">"</span><span style="color: #800000;">INIT_SECOND_STAGE</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">true</span><span style="color: #800000;">"</span>, <span style="color: #800080;">1</span><span style="color: #000000;">);

        </span><span style="color: #0000ff;">static</span> constexpr uint32_t kNanosecondsPerMillisecond =<span style="color: #000000;"> 1e6;
        uint64_t start_ms </span>= start_time.time_since_epoch().count() /<span style="color: #000000;"> kNanosecondsPerMillisecond;
        setenv(</span><span style="color: #800000;">"</span><span style="color: #800000;">INIT_STARTED_AT</span><span style="color: #800000;">"</span>, StringPrintf(<span style="color: #800000;">"</span><span style="color: #800000;">%</span><span style="color: #800000;">"</span> PRIu64, start_ms).c_str(), <span style="color: #800080;">1</span><span style="color: #000000;">);

        </span><span style="color: #0000ff;">char</span>* path = argv[<span style="color: #800080;">0</span><span style="color: #000000;">];
        </span><span style="color: #0000ff;">char</span>* args[] =<span style="color: #000000;"> { path, nullptr };
        execv(path, args);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> execv() only returns if an error happened, in which case we
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> panic and never fall through this conditional.</span>
        PLOG(ERROR) &lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">execv(\"</span><span style="color: #800000;">"</span> &lt;&lt; path &lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">\") failed</span><span style="color: #800000;">"</span><span style="color: #000000;">;
        security_failure();
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> At this point we're in the second stage of init.</span>
<span style="color: #000000;">    InitKernelLogging(argv);
    LOG(INFO) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">init second stage started!</span><span style="color: #800000;">"</span><span style="color: #000000;">;

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up a session keyring that all processes will have access to. It
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> will hold things like FBE encryption keys. No process should override
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> its session keyring.</span>
    keyctl(KEYCTL_GET_KEYRING_ID, KEY_SPEC_SESSION_KEYRING, <span style="color: #800080;">1</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Indicate that booting is in progress to background fw loaders, etc.</span>
    close(open(<span style="color: #800000;">"</span><span style="color: #800000;">/dev/.booting</span><span style="color: #800000;">"</span>, O_WRONLY | O_CREAT | O_CLOEXEC, <span style="color: #800080;">0000</span><span style="color: #000000;">));

    property_init();

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> If arguments are passed both on the command line and in DT,
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> properties set in DT always have priority over the command-line ones.</span>
<span style="color: #000000;">    process_kernel_dt();
    process_kernel_cmdline();

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Propagate the kernel variables to internal variables
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> used by init as well as the current required properties.</span>
<span style="color: #000000;">    export_kernel_boot_props();

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Make the time that init started available for bootstat to log.</span>
    property_set(<span style="color: #800000;">"</span><span style="color: #800000;">ro.boottime.init</span><span style="color: #800000;">"</span>, getenv(<span style="color: #800000;">"</span><span style="color: #800000;">INIT_STARTED_AT</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    property_set(</span><span style="color: #800000;">"</span><span style="color: #800000;">ro.boottime.init.selinux</span><span style="color: #800000;">"</span>, getenv(<span style="color: #800000;">"</span><span style="color: #800000;">INIT_SELINUX_TOOK</span><span style="color: #800000;">"</span><span style="color: #000000;">));

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set libavb version for Framework-only OTA match in Treble build.</span>
    <span style="color: #0000ff;">const</span> <span style="color: #0000ff;">char</span>* avb_version = getenv(<span style="color: #800000;">"</span><span style="color: #800000;">INIT_AVB_VERSION</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">if</span> (avb_version) property_set(<span style="color: #800000;">"</span><span style="color: #800000;">ro.boot.avb_version</span><span style="color: #800000;">"</span><span style="color: #000000;">, avb_version);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Clean up our environment.</span>
    unsetenv(<span style="color: #800000;">"</span><span style="color: #800000;">INIT_SECOND_STAGE</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    unsetenv(</span><span style="color: #800000;">"</span><span style="color: #800000;">INIT_STARTED_AT</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    unsetenv(</span><span style="color: #800000;">"</span><span style="color: #800000;">INIT_SELINUX_TOOK</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    unsetenv(</span><span style="color: #800000;">"</span><span style="color: #800000;">INIT_AVB_VERSION</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Now set up SELinux for second stage.</span>
    selinux_initialize(<span style="color: #0000ff;">false</span><span style="color: #000000;">);
    selinux_restore_context();

    epoll_fd </span>=<span style="color: #000000;"> epoll_create1(EPOLL_CLOEXEC);
    </span><span style="color: #0000ff;">if</span> (epoll_fd == -<span style="color: #800080;">1</span><span style="color: #000000;">) {
        PLOG(ERROR) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">epoll_create1 failed</span><span style="color: #800000;">"</span><span style="color: #000000;">;
        exit(</span><span style="color: #800080;">1</span><span style="color: #000000;">);
    }

    signal_handler_init();

    property_load_boot_defaults();
    export_oem_lock_status();
    start_property_service();
    set_usb_controller();

    </span><span style="color: #0000ff;">const</span><span style="color: #000000;"> BuiltinFunctionMap function_map;
    Action::set_function_map(</span>&amp;<span style="color: #000000;">function_map);

    Parser</span>&amp; parser =<span style="color: #000000;"> Parser::GetInstance();
    parser.AddSectionParser(</span><span style="color: #800000;">"</span><span style="color: #800000;">service</span><span style="color: #800000;">"</span>,std::make_unique&lt;ServiceParser&gt;<span style="color: #000000;">());
    parser.AddSectionParser(</span><span style="color: #800000;">"</span><span style="color: #800000;">on</span><span style="color: #800000;">"</span>, std::make_unique&lt;ActionParser&gt;<span style="color: #000000;">());
    parser.AddSectionParser(</span><span style="color: #800000;">"</span><span style="color: #800000;">import</span><span style="color: #800000;">"</span>, std::make_unique&lt;ImportParser&gt;<span style="color: #000000;">());
    std::</span><span style="color: #0000ff;">string</span> bootscript = GetProperty(<span style="color: #800000;">"</span><span style="color: #800000;">ro.boot.init_rc</span><span style="color: #800000;">"</span>, <span style="color: #800000;">""</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (bootscript.empty()) {
        parser.ParseConfig(</span><span style="color: #800000;">"</span><span style="color: #800000;">/init.rc</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        parser.set_is_system_etc_init_loaded(
                parser.ParseConfig(</span><span style="color: #800000;">"</span><span style="color: #800000;">/system/etc/init</span><span style="color: #800000;">"</span><span style="color: #000000;">));
        parser.set_is_vendor_etc_init_loaded(
                parser.ParseConfig(</span><span style="color: #800000;">"</span><span style="color: #800000;">/vendor/etc/init</span><span style="color: #800000;">"</span><span style="color: #000000;">));
        parser.set_is_odm_etc_init_loaded(parser.ParseConfig(</span><span style="color: #800000;">"</span><span style="color: #800000;">/odm/etc/init</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
        parser.ParseConfig(bootscript);
        parser.set_is_system_etc_init_loaded(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
        parser.set_is_vendor_etc_init_loaded(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
        parser.set_is_odm_etc_init_loaded(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Turning this on and letting the INFO logging be discarded adds 0.2s to
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Nexus 9 boot time, so it's disabled by default.</span>
    <span style="color: #0000ff;">if</span> (<span style="color: #0000ff;">false</span><span style="color: #000000;">) parser.DumpState();

    ActionManager</span>&amp; am =<span style="color: #000000;"> ActionManager::GetInstance();

    am.QueueEventTrigger(</span><span style="color: #800000;">"</span><span style="color: #800000;">early-init</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Queue an action that waits for coldboot done so we know ueventd has set up all of /dev...</span>
    am.QueueBuiltinAction(wait_for_coldboot_done_action, <span style="color: #800000;">"</span><span style="color: #800000;">wait_for_coldboot_done</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> ... so that we can start queuing up actions that require stuff from /dev.</span>
    am.QueueBuiltinAction(mix_hwrng_into_linux_rng_action, <span style="color: #800000;">"</span><span style="color: #800000;">mix_hwrng_into_linux_rng</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    am.QueueBuiltinAction(set_mmap_rnd_bits_action, </span><span style="color: #800000;">"</span><span style="color: #800000;">set_mmap_rnd_bits</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    am.QueueBuiltinAction(set_kptr_restrict_action, </span><span style="color: #800000;">"</span><span style="color: #800000;">set_kptr_restrict</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    am.QueueBuiltinAction(keychord_init_action, </span><span style="color: #800000;">"</span><span style="color: #800000;">keychord_init</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    am.QueueBuiltinAction(console_init_action, </span><span style="color: #800000;">"</span><span style="color: #800000;">console_init</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Trigger all the boot actions to get us started.</span>
    am.QueueEventTrigger(<span style="color: #800000;">"</span><span style="color: #800000;">init</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Repeat mix_hwrng_into_linux_rng in case /dev/hw_random or /dev/random
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> wasn't ready immediately after wait_for_coldboot_done</span>
    am.QueueBuiltinAction(mix_hwrng_into_linux_rng_action, <span style="color: #800000;">"</span><span style="color: #800000;">mix_hwrng_into_linux_rng</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Don't mount filesystems or start core system services in charger mode.</span>
    std::<span style="color: #0000ff;">string</span> bootmode = GetProperty(<span style="color: #800000;">"</span><span style="color: #800000;">ro.bootmode</span><span style="color: #800000;">"</span>, <span style="color: #800000;">""</span><span style="color: #000000;">);
    </span><span style="color: #0000ff;">if</span> (bootmode == <span style="color: #800000;">"</span><span style="color: #800000;">charger</span><span style="color: #800000;">"</span><span style="color: #000000;">) {
        am.QueueEventTrigger(</span><span style="color: #800000;">"</span><span style="color: #800000;">charger</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
        am.QueueEventTrigger(</span><span style="color: #800000;">"</span><span style="color: #800000;">late-init</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Run all property triggers based on current state of the properties.</span>
    am.QueueBuiltinAction(queue_property_triggers_action, <span style="color: #800000;">"</span><span style="color: #800000;">queue_property_triggers</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    </span><span style="color: #0000ff;">while</span> (<span style="color: #0000ff;">true</span><span style="color: #000000;">) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> By default, sleep until something happens.</span>
        <span style="color: #0000ff;">int</span> epoll_timeout_ms = -<span style="color: #800080;">1</span><span style="color: #000000;">;

        </span><span style="color: #0000ff;">if</span> (!(waiting_for_prop ||<span style="color: #000000;"> ServiceManager::GetInstance().IsWaitingForExec())) {
            am.ExecuteOneCommand();
        }
        </span><span style="color: #0000ff;">if</span> (!(waiting_for_prop ||<span style="color: #000000;"> ServiceManager::GetInstance().IsWaitingForExec())) {
            restart_processes();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If there's a process that needs restarting, wake up in time for that.</span>
            <span style="color: #0000ff;">if</span> (process_needs_restart_at != <span style="color: #800080;">0</span><span style="color: #000000;">) {
                epoll_timeout_ms </span>= (process_needs_restart_at - time(nullptr)) * <span style="color: #800080;">1000</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">if</span> (epoll_timeout_ms &lt; <span style="color: #800080;">0</span>) epoll_timeout_ms = <span style="color: #800080;">0</span><span style="color: #000000;">;
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> If there's more work to do, wake up again immediately.</span>
            <span style="color: #0000ff;">if</span> (am.HasMoreCommands()) epoll_timeout_ms = <span style="color: #800080;">0</span><span style="color: #000000;">;
        }

        epoll_event ev;
        </span><span style="color: #0000ff;">int</span> nr = TEMP_FAILURE_RETRY(epoll_wait(epoll_fd, &amp;ev, <span style="color: #800080;">1</span><span style="color: #000000;">, epoll_timeout_ms));
        </span><span style="color: #0000ff;">if</span> (nr == -<span style="color: #800080;">1</span><span style="color: #000000;">) {
            PLOG(ERROR) </span>&lt;&lt; <span style="color: #800000;">"</span><span style="color: #800000;">epoll_wait failed</span><span style="color: #800000;">"</span><span style="color: #000000;">;
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (nr == <span style="color: #800080;">1</span><span style="color: #000000;">) {
            ((</span><span style="color: #0000ff;">void</span> (*<span style="color: #000000;">)()) ev.data.ptr)();
        }
    }

    </span><span style="color: #0000ff;">return</span> <span style="color: #800080;">0</span><span style="color: #000000;">;
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>