---
title: Android 开源框架 ( 十一 ) 图片加载框架---Glide
date: 2018-08-05 14:07:01
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/Glide.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.引言</h2>
<p>&nbsp;Glide被广泛运用在google的开源项目中，包括2014年的google I/O大会上发布的官方app</p>
<div>&nbsp;前面介绍了</div>
<div>&nbsp; 　　Universal ImageLoader&nbsp;<a id="post_title_link_9425287" href="https://www.cnblogs.com/bugzone/p/Picasso.html" target="_blank">Android 开源框架 ( 十 ) 图片加载框架---Picasso</a></div>
<div>&nbsp; 　　Picasso.&nbsp;<a id="post_title_link_9425211" href="https://www.cnblogs.com/bugzone/p/ImageLoader.html" target="_blank">Android 开源框架 ( 九 ) 图片加载框架---ImageLoader</a></div>
<div>　　</div>
<div>　　而本文介绍的Glide是在Picasso基础上进行的二次开发，和Picasso 有90%相似度，其优势显而易见。Universal ImageLoader已停止服务。</div>
<div>&nbsp;</div>
<h2>二.基本使用</h2>
<h3>　　1.添加最新依赖　　</h3>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.github.bumptech.glide:glide:4.7.1</span><span style="color: #800000;">'</span></pre>
</div>
<p><strong>注意</strong>：Glide默认会导入Android的support-v4包。4.71版本默认导入的是v4包的27版本。如果你的项目中有v4包的别的版本，就会引起冲突发生错误如 java.lang.NoSuchMethodError: No static method。</p>
<h3>　　2.基本使用</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">Glide.with(mContext)
    .load(mDatas[position])
    .placeholder(R.mipmap.ic_launcher) </span><span style="color: #008000;">//</span><span style="color: #008000;">占位图</span>
    .error(R.mipmap.ic_launcher)  <span style="color: #008000;">//</span><span style="color: #008000;">出错的占位图</span>
    .<span style="color: #0000ff;">override</span>(width, height) <span style="color: #008000;">//</span><span style="color: #008000;">图片显示的分辨率 ，像素值 可以转化为DP再设置</span>
<span style="color: #000000;">    .animate(R.anim.glide_anim)
    .centerCrop()
    .fitCenter()
    .into(holder.image);</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>三.拓展了解Glide加载不同的图片</h2>
<p>　　Glide不仅仅可以加载网络图片，同样也能加载资源图片，本地图片，GIf, 视频快照，缩略图等。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;">（1）加载网络图片</span>
    Glide.with(<span style="color: #0000ff;">this</span>).load(<span style="color: #800000;">"</span><span style="color: #800000;">http://img1.imgtn.bdimg.com/it/u=2615772929,948758168&amp;fm=21&amp;gp=0.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">).into(ivGlide1);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（2）加载资源图片</span>
    Glide.with(<span style="color: #0000ff;">this</span><span style="color: #000000;">).load(R.drawable.atguigu_logo).into(ivGlide2);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（3）加载本地图片</span>
    String path = Environment.getExternalStorageDirectory() + <span style="color: #800000;">"</span><span style="color: #800000;">/meinv1.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    File file </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(path);
    Uri uri </span>=<span style="color: #000000;"> Uri.fromFile(file);
    Glide.with(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">).load(uri).into(ivGlide3);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> （4）加载网络gif</span>
    String gifUrl = <span style="color: #800000;">"</span><span style="color: #800000;">http://b.hiphotos.baidu.com/zhidao/pic/item/faedab64034f78f066abccc57b310a55b3191c67.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    Glide.with(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">).load(gifUrl).placeholder(R.mipmap.ic_launcher).into(ivGlide4);

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> （5）加载资源gif</span>
    Glide.with(<span style="color: #0000ff;">this</span><span style="color: #000000;">).load(R.drawable.loading).asGif().placeholder(R.mipmap.ic_launcher).into(ivGlide5);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（6）加载本地gif</span>
    String gifPath = Environment.getExternalStorageDirectory() + <span style="color: #800000;">"</span><span style="color: #800000;">/meinv2.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    File gifFile </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(gifPath);
    Glide.with(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">).load(gifFile).placeholder(R.mipmap.ic_launcher).into(ivGlide6);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（7）加载本地小视频和快照</span>
    String videoPath = Environment.getExternalStorageDirectory() + <span style="color: #800000;">"</span><span style="color: #800000;">/video.mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    File videoFile </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(videoPath);
    Glide.with(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">).load(Uri.fromFile(videoFile)).placeholder(R.mipmap.ic_launcher).into(ivGlide7);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（8）设置缩略图比例,然后，先加载缩略图，再加载原图</span>
    String urlPath = Environment.getExternalStorageDirectory() + <span style="color: #800000;">"</span><span style="color: #800000;">/meinv1.jpg</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    Glide.with(</span><span style="color: #0000ff;">this</span>).load(<span style="color: #0000ff;">new</span> File(urlPath)).thumbnail(<span style="color: #800080;">0.1f</span><span style="color: #000000;">).centerCrop().placeholder(R.mipmap.ic_launcher).into(ivGlide8);

    </span><span style="color: #008000;">//</span><span style="color: #008000;">（9）先建立一个缩略图对象，然后，先加载缩略图，再加载原图</span>
    DrawableRequestBuilder thumbnailRequest = Glide.with(<span style="color: #0000ff;">this</span>).load(<span style="color: #0000ff;">new</span><span style="color: #000000;"> File(urlPath));
    Glide.with(</span><span style="color: #0000ff;">this</span>).load(Uri.fromFile(videoFile)).thumbnail(thumbnailRequest).centerCrop().placeholder(R.mipmap.ic_launcher).into(ivGlide9);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>