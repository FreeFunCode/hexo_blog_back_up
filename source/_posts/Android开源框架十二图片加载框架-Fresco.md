---
title: Android 开源框架 ( 十二 ) 图片加载框架---Fresco
date: 2018-08-05 18:43:27
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/Fresco.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　Fresco是Facebook推出的一款用于Android应用中展示图片的强大图片库。它具有强大的内存管理、渐进式呈现图片、支持加载Gif图和WebP格式等特点。</p>
<p>　　相比于其他图片框架要庞大的多，但是对于图片的处理也要比其他框架支持的多。</p>
<p>　　Fresco官方中文介绍文档：<a href="https://www.fresco-cn.org/" target="_blank">https://www.fresco-cn.org/</a></p>
<p>　　Fresco GitHub 地址：<a href="https://github.com/facebook/fresco" target="_blank">https://github.com/facebook/fresco</a></p>
<h2>Fresco使用</h2>
<h3>　　1.添加依赖</h3>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.facebook.fresco:fresco:1.10.0</span><span style="color: #800000;">'</span></pre>
</div>
<h4>　　其他相关依赖</h4>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.facebook.fresco:animated-gif:1.5.0</span><span style="color: #800000;">'</span><span style="color: #008000;">//</span><span style="color: #008000;">加载gif动图需添加此库</span>
compile <span style="color: #800000;">'</span><span style="color: #800000;">com.facebook.fresco:animated-webp:1.5.0</span><span style="color: #800000;">'</span><span style="color: #008000;">//</span><span style="color: #008000;">加载webp动图需添加此库</span>
compile <span style="color: #800000;">'</span><span style="color: #800000;">com.facebook.fresco:webpsupport:1.5.0</span><span style="color: #800000;">'</span><span style="color: #008000;">//</span><span style="color: #008000;">支持webp需添加此库</span>
compile <span style="color: #800000;">'</span><span style="color: #800000;">com.facebook.fresco:imagepipeline-okhttp3:1.5.0</span><span style="color: #800000;">'</span><span style="color: #008000;">//</span><span style="color: #008000;">网络实现层使用okhttp3需添加此库</span>
compile <span style="color: #800000;">'</span><span style="color: #800000;">jp.wasabeef:fresco-processors:2.1.0@aar</span><span style="color: #800000;">'</span><span style="color: #008000;">//</span><span style="color: #008000;">用于提供fresco的各种图片变换</span></pre>
</div>
<h3>　　2.图片加载布局控件</h3>
<p>　　必须设置layout_width、layout_height两个属性，否则无法展示。并且注意SimpleDraweeView不支持wrap_content属性。可以参考官方文档：<a href="https://www.fresco-cn.org/docs/wrap-content.html" target="_blank">wrap_content的限制</a> 。你必须指定尺寸或者用<code class="highlighter-rouge">match_parent</code>来布局。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>&lt;<span style="color: #000000;">com.facebook.drawee.view.SimpleDraweeView
        android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/sdv_fresco</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">130dp</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_gravity</span>=<span style="color: #800000;">"</span><span style="color: #800000;">center</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">130dp</span><span style="color: #800000;">"</span><span style="color: #000000;">
        fresco:placeholderImage</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@drawable/atguigu_logo</span><span style="color: #800000;">"</span> /&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>　　3.常用属性</h2>
<h4>　　3.1&nbsp;带进度条的图片</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;"> 设置样式</span>
    GenericDraweeHierarchyBuilder builder = <span style="color: #0000ff;">new</span><span style="color: #000000;"> GenericDraweeHierarchyBuilder(getResources());
    GenericDraweeHierarchy hierarchy </span>= builder.setProgressBarImage(<span style="color: #0000ff;">new</span><span style="color: #000000;"> ProgressBarDrawable()).build();
    sdvFresco.setHierarchy(hierarchy);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片的地址</span>
    Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
    sdvFresco.setImageURI(uri);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.2&nbsp;图片的不同裁剪</h4>
<p>　　　　CENTER</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>　　GenericDraweeHierarchyBuilder builder = <span style="color: #0000ff;">new</span><span style="color: #000000;"> GenericDraweeHierarchyBuilder(getResources());
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">居中，无缩放</span>
    GenericDraweeHierarchy hierarchy =<span style="color: #000000;"> builder.setActualImageScaleType(ScalingUtils.ScaleType.CENTER).build();</span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置样式</span>
<span style="color: #000000;">    sdvFresco.setHierarchy(hierarchy);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片的地址</span>
    Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
    sdvFresco.setImageURI(uri);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　CENTER_CROP</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">保持宽高比缩小或放大，使得两边都大于或等于显示边界。居中显示</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.CENTER_CROP).build();</pre>
</div>
<p>　　FOCUS_CROP</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> 同centerCrop, 但居中点不是中点，而是指定的某个点,这里我设置为图片的左上角那点</span>
    PointF point = <span style="color: #0000ff;">new</span> PointF(<span style="color: #800080;">0</span>,<span style="color: #800080;">0</span><span style="color: #000000;">);
    GenericDraweeHierarchy hierarchy </span>= builder.setActualImageScaleType(ScalingUtils.ScaleType.FOCUS_CROP)</pre>
</div>
<p>　　CENTER_INSIDE</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">使两边都在显示边界内，居中显示。如果图尺寸大于显示边界，则保持长宽比缩小图片</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.CENTER_INSIDE).build();</pre>
</div>
<p>　　FIT_CENTER</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">保持宽高比，缩小或者放大，使得图片完全显示在显示边界内。居中显示</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.FIT_CENTER).build();</pre>
</div>
<p>　　FIT_START</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">保持宽高比，缩小或者放大，使得图片完全显示在显示边界内，不居中，和显示边界左上对齐</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.FIT_START).build();</pre>
</div>
<p>　　FIT_END</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">保持宽高比，缩小或者放大，使得图片完全显示在显示边界内，不居中，和显示边界右下对齐</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.FIT_END).build();</pre>
</div>
<p>　　FIT_XY</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">不保持宽高比，填充满显示边界</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(ScalingUtils.ScaleType.FIT_XY).build();</pre>
</div>
<p>　　title mode</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">如要使用title mode显示, 需要设置为none</span>
    GenericDraweeHierarchy hierarchy = builder.setActualImageScaleType(<span style="color: #0000ff;">null</span>).build();</pre>
</div>
<h4>　　3.3&nbsp;圆形和圆角图片　　</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;">圆形图片</span>
    parames =<span style="color: #000000;"> RoundingParams.asCircle();
    GenericDraweeHierarchy hierarchy </span>=<span style="color: #000000;"> builder.setRoundingParams(parames).build();
    
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">圆角图片</span>
    parames =<span style="color: #000000;"> RoundingParams.fromCornersRadius(50f);
    </span><span style="color: #008000;">//</span><span style="color: #008000;">parames.setOverlayColor(getResources().getColor(android.R.color.holo_red_light));</span><span style="color: #008000;">//</span><span style="color: #008000;">覆盖层
    </span><span style="color: #008000;">//</span><span style="color: #008000;">parames.setBorder(getResources().getColor(android.R.color.holo_blue_light), 5);</span><span style="color: #008000;">//</span><span style="color: #008000;">边框</span>
    GenericDraweeHierarchy hierarchy = builder.setRoundingParams(parames).build();</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.4&nbsp;渐进式展示图片</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;"> 加载质量配置</span>
    ProgressiveJpegConfig jpegConfig = <span style="color: #0000ff;">new</span><span style="color: #000000;"> ProgressiveJpegConfig() {
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">int</span> getNextScanNumberToDecode(<span style="color: #0000ff;">int</span><span style="color: #000000;"> scanNumber) {
            </span><span style="color: #0000ff;">return</span> scanNumber + <span style="color: #800080;">2</span><span style="color: #000000;">;
        }

        @Override
        </span><span style="color: #0000ff;">public</span> QualityInfo getQualityInfo(<span style="color: #0000ff;">int</span><span style="color: #000000;"> scanNumber) {
            boolean isGoodEnough </span>= (scanNumber &gt;= <span style="color: #800080;">5</span><span style="color: #000000;">);

            </span><span style="color: #0000ff;">return</span> ImmutableQualityInfo.of(scanNumber, isGoodEnough, <span style="color: #0000ff;">false</span><span style="color: #000000;">);
        }
    };

    ImagePipelineConfig.newBuilder(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">).setProgressiveJpegConfig(jpegConfig).build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 获取图片URL</span>
    Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 获取图片请求</span>
    ImageRequest request = ImageRequestBuilder.newBuilderWithSource(uri).setProgressiveRenderingEnabled(<span style="color: #0000ff;">true</span><span style="color: #000000;">).build();
    DraweeController draweeController </span>=<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setImageRequest(request)
            .setTapToRetryEnabled(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">)
            .setOldController(sdvFresco.getController())</span><span style="color: #008000;">//</span><span style="color: #008000;">使用oldController可以节省不必要的内存分配</span>
<span style="color: #000000;">            .build();

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置加载的控制</span>
    sdvFresco.setController(draweeController);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.5&nbsp;GIF动画图片</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>　　Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    DraweeController controller </span>=<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setUri(uri)
            .setAutoPlayAnimations(</span><span style="color: #0000ff;">true</span>) <span style="color: #008000;">//</span><span style="color: #008000;">是否自动播放</span>
<span style="color: #000000;">            .setOldController(sdvFresco.getController())
            .build();

    sdvFresco.setController(controller);
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 动画开始</span>
    Animatable animatable =<span style="color: #000000;"> sdvFresco.getController().getAnimatable();
    </span><span style="color: #0000ff;">if</span>(animatable != <span style="color: #0000ff;">null</span> &amp;&amp; !<span style="color: #000000;">animatable.isRunning()) {
        animatable.start();
    }
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;">动画停止</span>
     Animatable animatable =<span style="color: #000000;"> sdvFresco.getController().getAnimatable();
    </span><span style="color: #0000ff;">if</span>(animatable != <span style="color: #0000ff;">null</span> &amp;&amp;<span style="color: #000000;"> animatable.isRunning()) {
        animatable.stop();
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.6&nbsp;多图请求及图片复用</h4>
<p>　　　　先显示低分辨率的图，然后是高分辨率的图</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;"> 图片地址</span>
    Uri lowUri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    Uri highUri </span>= Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 控制加载图片</span>
    DraweeController controller =<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setLowResImageRequest(ImageRequest.fromUri(lowUri))
            .setImageRequest(ImageRequest.fromUri(highUri))
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
<span style="color: #000000;">    sdvFresco.setController(controller);
    
    本地缩略图预览
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 图片地址</span>
    Uri uri = Uri.fromFile(<span style="color: #0000ff;">new</span> File(Environment.getExternalStorageDirectory() +<span style="color: #800000;">"</span><span style="color: #800000;">/meinv1.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片的请求</span>
    ImageRequest request =<span style="color: #000000;"> ImageRequestBuilder.newBuilderWithSource(uri)
            .setLocalThumbnailPreviewsEnabled(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 控制图片的加载</span>
    DraweeController controller =<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setImageRequest(request)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
<span style="color: #000000;">    sdvFresco.setController(controller);
    
    本地图片复用
    </span><span style="color: #008000;">//</span><span style="color: #008000;">本地图片的复用
    </span><span style="color: #008000;">//</span><span style="color: #008000;">在请求之前，还会去内存中请求一次图片，没有才会先去本地，最后去网络uri
    </span><span style="color: #008000;">//</span><span style="color: #008000;">本地准备复用图片的uri  如果本地这个图片不存在，会自动去加载下一个uri
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 请求加载图片</span>
    Uri uri1 = Uri.fromFile(<span style="color: #0000ff;">new</span> File(Environment.getExternalStorageDirectory()+<span style="color: #800000;">"</span><span style="color: #800000;">/meinv.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    </span><span style="color: #008000;">//</span><span style="color: #008000;">图片的网络uri</span>
    Uri uri2 = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    ImageRequest request1 </span>=<span style="color: #000000;"> ImageRequest.fromUri(uri1);
    ImageRequest request2 </span>=<span style="color: #000000;"> ImageRequest.fromUri(uri2);
    ImageRequest[] requests </span>=<span style="color: #000000;"> {request1, request2};
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 控制加载图片</span>
    DraweeController controller =<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setFirstAvailableImageRequests(requests)
            .setOldController(sdvFresco.getController())
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
    sdvFresco.setController(controller);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.7&nbsp;图片加载监听</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #008000;">//</span><span style="color: #008000;"> 图片加载的控制
     </span><span style="color: #008000;">//</span><span style="color: #008000;">添加监听事件 .setControllerListener(controllerListener)</span>
<span style="color: #000000;">    SimpleDraweeView sdvFrescoListener;
     
    DraweeController controller </span>=<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setOldController(sdvFrescoListener.getController())
            .setImageRequest(request)
            .setControllerListener(controllerListener)
            .build();</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">private</span> ControllerListener controllerListener = <span style="color: #0000ff;">new</span> BaseControllerListener&lt;ImageInfo&gt;<span style="color: #000000;">(){
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片完毕</span>
<span style="color: #000000;">        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onFinalImageSet(String id, ImageInfo imageInfo, Animatable animatable) {
            super.onFinalImageSet(id, imageInfo, animatable);

            </span><span style="color: #0000ff;">if</span> (imageInfo == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
            }

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 获取图片的质量</span>
            QualityInfo qualityInfo =<span style="color: #000000;"> imageInfo.getQualityInfo();

            </span><span style="color: #008000;">//</span><span style="color: #008000;">页面文本打印setText</span>
            tvFrescoListener.setText(<span style="color: #800000;">"</span><span style="color: #800000;">Final image received! </span><span style="color: #800000;">"</span> +
                    <span style="color: #800000;">"</span><span style="color: #800000;">\nSize: </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> imageInfo.getWidth()
                    </span>+ <span style="color: #800000;">"</span><span style="color: #800000;">x</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> imageInfo.getHeight()
                    </span>+ <span style="color: #800000;">"</span><span style="color: #800000;">\nQuality level: </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> qualityInfo.getQuality()
                    </span>+ <span style="color: #800000;">"</span><span style="color: #800000;">\ngood enough: </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> qualityInfo.isOfGoodEnoughQuality()
                    </span>+ <span style="color: #800000;">"</span><span style="color: #800000;">\nfull quality: </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> qualityInfo.isOfFullQuality());
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 渐进式加载图片回调</span>
<span style="color: #000000;">        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onIntermediateImageSet(String id, ImageInfo imageInfo) {
            super.onIntermediateImageSet(id, imageInfo);

            tvFrescoListener2.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">IntermediateImageSet image receiced</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片失败</span>
<span style="color: #000000;">        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onFailure(String id, Throwable throwable) {
            super.onFailure(id, throwable);
            
            tvFrescoListener.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">Error loading</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> id);
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.8&nbsp;图片修改和旋转</h4>
<p>　　　　修内存中改图片大小 setResizeOptions&nbsp;</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre> 　　<span style="color: #008000;">//</span><span style="color: #008000;"> 图片地址</span>
    Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 图片的请求</span>
    ImageRequest request =<span style="color: #000000;"> ImageRequestBuilder.newBuilderWithSource(uri)
            .setResizeOptions(</span><span style="color: #0000ff;">new</span> ResizeOptions(<span style="color: #800080;">50</span>,<span style="color: #800080;">50</span><span style="color: #000000;">))
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 控制图片的加载</span>
    PipelineDraweeController controller =<span style="color: #000000;"> (PipelineDraweeController) Fresco.newDraweeControllerBuilder()
            .setOldController(sdvFresco.getController())
            .setImageRequest(request)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
    sdvFresco.setController(controller);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　　　旋转图片 setAutoRotateEnabled</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>　　Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    ImageRequest request </span>=<span style="color: #000000;"> ImageRequestBuilder.newBuilderWithSource(uri)
            .setAutoRotateEnabled(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 控制图片的加载</span>
    DraweeController controller =<span style="color: #000000;"> Fresco.newDraweeControllerBuilder()
            .setOldController(sdvFresco.getController())
            .setImageRequest(request)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
    sdvFresco.setController(controller);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　3.9&nbsp;修改图片(显示过程同上,主要更改了ImageRequest设置) setPostprocessor</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #008000;">//</span><span style="color: #008000;"> 修改图片</span>
    Postprocessor postProcessor = <span style="color: #0000ff;">new</span><span style="color: #000000;"> BasePostprocessor() {
        @Override
        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> String getName() {
            </span><span style="color: #0000ff;">return</span> <span style="color: #800000;">"</span><span style="color: #800000;">postProcessor</span><span style="color: #800000;">"</span><span style="color: #000000;">;
        }
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> process(Bitmap bitmap) {
            </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> x = <span style="color: #800080;">0</span>; x &lt; bitmap.getWidth(); x += <span style="color: #800080;">2</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> y = <span style="color: #800080;">0</span>; y &lt; bitmap.getHeight(); y += <span style="color: #800080;">2</span><span style="color: #000000;">) {
                    bitmap.setPixel(x, y, Color.RED);
                }
            }
        }
    };

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 创建图片请求</span>
    ImageRequest request =<span style="color: #000000;"> ImageRequestBuilder.newBuilderWithSource(uri)
            .setPostprocessor(postProcessor)
            .build();</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>&nbsp;　　3.10&nbsp;动态展示图片（添加SimpleDraweeView到LinearLayout中）</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>　　SimpleDraweeView simpleDraweeView = <span style="color: #0000ff;">new</span> SimpleDraweeView(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置宽高比</span>
    simpleDraweeView.setAspectRatio(<span style="color: #800080;">3.0f</span><span style="color: #000000;">);
    
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 图片的地址</span>
    Uri uri = Uri.parse(<span style="color: #800000;">"</span><span style="color: #800000;">http://</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 图片的请求</span>
    ImageRequest request =<span style="color: #000000;"> ImageRequestBuilder.newBuilderWithSource(uri)
            .build();
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片的控制</span>
    PipelineDraweeController controller =<span style="color: #000000;"> (PipelineDraweeController) Fresco.newDraweeControllerBuilder()
            .setOldController(simpleDraweeView.getController())
            .setImageRequest(request)
            .build();

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
<span style="color: #000000;">    simpleDraweeView.setController(controller);
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 添加View到线性布局中</span>
    linearLayout.addView(simpleDraweeView);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h4>&nbsp;</h4>
</div>