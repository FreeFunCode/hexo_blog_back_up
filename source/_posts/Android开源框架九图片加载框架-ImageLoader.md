---
title: Android 开源框架 ( 九 ) 图片加载框架---ImageLoader
date: 2018-08-05 13:00:34
tags: 开源框架
categories: 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/ImageLoader.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.引言</h2>
<p>　　Android的每一个App通常只拥有有限的系统资源，Android设备为每个App分配的内存大小是也是有上限的，并且，针对不同的设备配置所分配的内存大小也是不一样的，最小为16MB。图片会占用大量的内存，尤其是那些超清照片。所以图片加载时做容易造成安卓内存溢出的原因，而要解决这些问题还需要很多相关知识：<br>　　1、多线程下载，线程管理。<br>　　2、多级缓存架构设计和策略，内存缓存，磁盘缓存，缓存有效性处理。<br>　　3、图片压缩，特效处理，动画处理。<br>　　4、复杂网络情况下下载图片策略，例如弱网络等。<br>　　5、内存管理，lru 算法、对象引用、GC回收等优化。</p>
<p>　　Universal ImageLoader 是很早开源的图片缓存，在早期被很多应用使用。</p>
<p>　　Universal ImageLoader的GitHub地址：<a href="https://github.com/nostra13/Android-Universal-Image-Loader" target="_blank">https://github.com/nostra13/Android-Universal-Image-Loader</a><br>　　最近一次更新是在3年前,已经停止更新了。不推荐新项目中使用。</p>
<h2>二.基本使用　</h2>
<h3>　　1. 添加依赖</h3>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.nostra13.universalimageloader:universal-image-loader:1.9.5</span><span style="color: #800000;">'</span></pre>
</div>
<h3>　　2.Application初始化</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　　　//</span><span style="color: #008000;"> 初始化参数</span>
        ImageLoaderConfiguration config = <span style="color: #0000ff;">new</span><span style="color: #000000;"> ImageLoaderConfiguration.Builder(context)
                .threadPriority(Thread.NORM_PRIORITY </span>- <span style="color: #800080;">2</span>)               <span style="color: #008000;">//</span><span style="color: #008000;"> 线程优先级</span>
                .denyCacheImageMultipleSizesInMemory()                  <span style="color: #008000;">//</span><span style="color: #008000;"> 当同一个Uri获取不同大小的图片，缓存到内存时，只缓存一个。默认会缓存多个不同的大小的相同图片</span>
                .discCacheFileNameGenerator(<span style="color: #0000ff;">new</span> Md5FileNameGenerator()) <span style="color: #008000;">//</span><span style="color: #008000;"> 将保存的时候的URI名称用MD5</span>
                .tasksProcessingOrder(QueueProcessingType.LIFO)         <span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片下载和显示的工作队列排序</span>
                .writeDebugLogs()                                       <span style="color: #008000;">//</span><span style="color: #008000;"> 打印debug log</span>
<span style="color: #000000;">                .build();

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 全局初始化此配置</span>
        ImageLoader.getInstance().init(config);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　3.基本使用</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">imageLoader.displayImage(Constants.IMAGES[position],holder.iv,options);

</span><span style="color: #0000ff;">private</span> DisplayImageOptions options = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DisplayImageOptions.Builder()
            .showStubImage(R.drawable.atguigu_logo)          </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片下载期间显示的图片</span>
            .showImageForEmptyUri(R.drawable.atguigu_logo)  <span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片Uri为空或是错误的时候显示的图片</span>
            .showImageOnFail(R.drawable.atguigu_logo)       <span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片加载或解码过程中发生错误显示的图片</span>
            .cacheInMemory(<span style="color: #0000ff;">true</span>)                        <span style="color: #008000;">//</span><span style="color: #008000;"> 设置下载的图片是否缓存在内存中</span>
            .cacheOnDisk(<span style="color: #0000ff;">true</span>)                          <span style="color: #008000;">//</span><span style="color: #008000;"> 设置下载的图片是否缓存在SD卡中</span>
            .displayer(<span style="color: #0000ff;">new</span> RoundedBitmapDisplayer(<span style="color: #800080;">20</span>))  <span style="color: #008000;">//</span><span style="color: #008000;"> 设置成圆角图片</span>
            .build();                                   <span style="color: #008000;">//</span><span style="color: #008000;"> 创建配置过得DisplayImageOption对象;        </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>三.拓展了解</h2>
<p>　　<strong>ImageLoaderConfiguration</strong>是针对图片缓存的全局配置，主要有线程类、缓存大小、磁盘大小、图片下载与解析、日志方面的配置。<br>　　<strong>ImageLoader</strong>是具体下载图片，缓存图片，显示图片的具体执行类，它有两个具体的方法displayImage(...)、loadImage(...)，但是其实最终他们的实现都是displayImage(...)。<br>　　<strong>DisplayImageOptions</strong>用于指导每一个Imageloader根据网络图片的状态（空白、下载错误、正在下载）显示对应的图片，是否将缓存加载到磁盘上，下载完后对图片进行怎么样的处理。</p>
<h4>　　1.ImageLoaderConfiguration的配置主要是全局性的配置，主要有线程类、缓存大小、磁盘大小、图片下载与解析、日志方面的配置。</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span><span style="color: #000000;"> ImageLoaderConfiguration config;
    config </span>= <span style="color: #0000ff;">new</span> ImageLoaderConfiguration.Builder(context)<span style="color: #008000;">//</span><span style="color: #008000;"> 开始构建 ,图片加载配置</span>
            .threadPriority(Thread.NORM_PRIORITY - <span style="color: #800080;">2</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 设置线程优先级</span>
            .threadPoolSize(<span style="color: #800080;">3</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 线程池内加载的数量 ;减少配置之中线程池的大小，(.threadPoolSize).推荐1-5；</span>
            .denyCacheImageMultipleSizesInMemory()<span style="color: #008000;">//</span><span style="color: #008000;"> 设置加载的图片有多样的</span>
            .tasksProcessingOrder(QueueProcessingType.LIFO)<span style="color: #008000;">//</span><span style="color: #008000;"> 图片加载任务顺序</span>
            .memoryCache(<span style="color: #0000ff;">new</span> WeakMemoryCache())<span style="color: #008000;">//</span><span style="color: #008000;">使用.memoryCache(new WeakMemoryCache())，不要使用.cacheInMemory();</span>
            .memoryCacheExtraOptions(<span style="color: #800080;">480</span>, <span style="color: #800080;">800</span>) <span style="color: #008000;">//</span><span style="color: #008000;"> 即保存的每个缓存文件的最大长宽</span>
            .memoryCacheSizePercentage(<span style="color: #800080;">60</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 图片内存占应用的60%；</span>
             .diskCacheFileNameGenerator(<span style="color: #0000ff;">new</span> HashCodeFileNameGenerator())<span style="color: #008000;">//</span><span style="color: #008000;">使用HASHCODE对UIL进行加密命名</span>
            .diskCacheFileNameGenerator(<span style="color: #0000ff;">new</span> Md5FileNameGenerator())<span style="color: #008000;">//</span><span style="color: #008000;"> 将保存的时候的URI名称用MD5 加密</span>
            .diskCacheSize(<span style="color: #800080;">50</span> * <span style="color: #800080;">1024</span> * <span style="color: #800080;">1024</span>) <span style="color: #008000;">//</span><span style="color: #008000;"> 缓存设置大小为50 Mb</span>
            .diskCache(<span style="color: #0000ff;">new</span> UnlimitedDiskCache(cacheDir))<span style="color: #008000;">//</span><span style="color: #008000;"> 自定义缓存路径</span>
            .diskCacheFileCount(<span style="color: #800080;">100</span>) <span style="color: #008000;">//</span><span style="color: #008000;"> 缓存的文件数量</span>
            .denyCacheImageMultipleSizesInMemory()<span style="color: #008000;">//</span><span style="color: #008000;"> 自动缩放</span>
            .imageDownloader(<span style="color: #0000ff;">new</span> BaseImageDownloader(context, <span style="color: #800080;">5</span> * <span style="color: #800080;">1000</span>, <span style="color: #800080;">30</span> * <span style="color: #800080;">1000</span>)) <span style="color: #008000;">//</span><span style="color: #008000;"> connectTimeout (5 s), readTimeout (30 s)超时时间</span>
            .memoryCacheExtraOptions(<span style="color: #800080;">480</span>, <span style="color: #800080;">800</span>)<span style="color: #008000;">//</span><span style="color: #008000;">设置缓存图片时候的宽高最大值，默认为屏幕宽高;保存的每个缓存文件的最大长宽</span>
            .defaultDisplayImageOptions(options)<span style="color: #008000;">//</span><span style="color: #008000;"> 如果需要打开缓存机制，需要自己builde一个option,可以是DisplayImageOptions.createSimple()</span>
            .writeDebugLogs() <span style="color: #008000;">//</span><span style="color: #008000;"> Remove for release app</span>
            .build();构建完成（参数可以不用设置全，根据需要来配置）        </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　2.每一个ImageLoader.displayImage(...)都可以使用Display Options。</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">    private</span><span style="color: #000000;"> DisplayImageOptions options;
    options </span>= <span style="color: #0000ff;">new</span> DisplayImageOptions.Builder()<span style="color: #008000;">//</span><span style="color: #008000;"> 开始构建, 显示的图片的各种格式</span>
                .resetViewBeforeLoading(<span style="color: #0000ff;">true</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片在下载前是否重置，复位</span>
                .cacheInMemory(<span style="color: #0000ff;">true</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 开启内存缓存</span>
                .cacheOnDisk(<span style="color: #0000ff;">true</span>) <span style="color: #008000;">//</span><span style="color: #008000;"> 开启硬盘缓存</span>
                 .displayer(<span style="color: #0000ff;">new</span> RoundedBitmapDisplayer(<span style="color: #800080;">20</span>))<span style="color: #008000;">//</span><span style="color: #008000;"> 是否设置为圆角，弧度为多少；避免使用RoundedBitmapDisplayer.他会创建新的ARGB_8888格式的Bitmap对象；</span>
                .displayer(<span style="color: #0000ff;">new</span> FadeInBitmapDisplayer(<span style="color: #800080;">100</span>))<span style="color: #008000;">//</span><span style="color: #008000;"> 是否图片加载好后渐入的动画时间</span>
                .displayer(<span style="color: #0000ff;">new</span> SimpleBitmapDisplayer())<span style="color: #008000;">//</span><span style="color: #008000;"> 正常显示一张图片　</span>
                .bitmapConfig(Bitmap.Config.RGB_565)<span style="color: #008000;">//</span><span style="color: #008000;"> 设置图片的解码类型;使用.bitmapConfig(Bitmap.config.RGB_565)代替ARGB_8888;</span>
                .considerExifParams(<span style="color: #0000ff;">true</span>)<span style="color: #008000;">//</span><span style="color: #008000;"> 是否考虑JPEG图像EXIF参数（旋转，翻转）</span>
                .imageScaleType(ImageScaleType.EXACTLY)<span style="color: #008000;">//</span><span style="color: #008000;"> 缩放级别</span>
                .imageScaleType(ImageScaleType.IN_SAMPLE_INT)<span style="color: #008000;">//</span><span style="color: #008000;">这两种配置缩放都推荐</span>
                .build();<span style="color: #008000;">//</span><span style="color: #008000;"> 构建完成（参数可以不用设置全，根据需要来配置）</span>
    ImageLoader.getInstance().init(config);//初始化完成</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.ImageLoader是显示图片的具体执行类，它有两个具体的方法displayImage loadImage(...)</h4>
<div class="cnblogs_code">
<pre>ImageLoader.getInstance().displayImage(url, imageView, options);    </pre>
</div>
<p>&nbsp;</p>
<p>　</p>
</div>