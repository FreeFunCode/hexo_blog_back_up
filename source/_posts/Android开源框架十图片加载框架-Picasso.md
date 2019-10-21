---
title: Android 开源框架 ( 十 ) 图片加载框架---Picasso
date: 2018-08-05 13:56:31
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/Picasso.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　Picasso 是Square 公司（SQUARE美国一家移动支付公司）开源的Android 端的图片加载和缓存框架。Square 还开源了Rerefoit 、OkHttp、LeakCanary、Picasso等等都是非常火的开源项目。</p>
<p>　　Picasso官方介绍网站：<a href="http://square.github.io/picasso/" target="_blank">http://square.github.io/picasso/</a></p>
<h2>一.基本使用</h2>
<p>　　1.添加依赖</p>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.squareup.picasso:picasso:2.5.2</span><span style="color: #800000;">'</span></pre>
</div>
<p>　　如果需要对加载的图片进行个性化处理可以(转换器Transformation)，添加图片处理的依赖库</p>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">jp.wasabeef:picasso-transformations:2.1.0</span><span style="color: #800000;">'</span>
    <span style="color: #008000;">//</span><span style="color: #008000;"> If you want to use the GPU Filters</span>
compile <span style="color: #800000;">'</span><span style="color: #800000;">jp.co.cyberagent.android.gpuimage:gpuimage-library:1.4.1</span><span style="color: #800000;">'</span></pre>
</div>
<p>&nbsp; &nbsp; 2.基本使用</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #008000;">//</span><span style="color: #008000;"> 加载图片</span>
<span style="color: #000000;">    Picasso.with(mContext)
            .load(url)
            .placeholder(R.drawable.ic_launcher)
            .error(R.drawable.ic_launcher)
            .into(holder.iv);        </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>二.Picasso其他设置</h2>
<div>　　<strong>1.设置的默认图</strong></div>
<div>
<div class="cnblogs_code">
<pre>.placeholder(R.drawable.default_bg)</pre>
</div>
<p>　　<strong>2.调用into的时候明确告诉你没有占位图设置。placeholder和noPlaceholder 不能同时应用在同一个请求上，否则会抛异常。</strong></p>
</div>
<div>
<div class="cnblogs_code">
<pre>.noPlaceholder()</pre>
</div>
<p>　　<strong>3.加载图片出错的情况下显示的默认图</strong></p>
</div>
<div>
<div class="cnblogs_code">
<pre>.error(R.drawable.error_iamge)</pre>
</div>
<p><strong>　　4.into 显示到ImageView 都会有一个简单的渐入过度效果,提升用户体验。如果不需要就设置noFade。默认会有渐变.</strong></p>
</div>
<div>
<div class="cnblogs_code">
<pre>.noFade()</pre>
</div>
<p><strong>　　5.设置图片尺寸(Resize)</strong></p>
</div>
<div>
<div class="cnblogs_code">
<pre>.resize(<span style="color: #800080;">400</span>,<span style="color: #800080;">200</span>) <span style="color: #008000;">//</span><span style="color: #008000;">该单位是pixels(px)</span>
.resizeDimen(R.dimen.image_width,R.dimen.image_height) <span style="color: #008000;">//</span><span style="color: #008000;">resizeDimen(int targetWidthResId, int targetHeightResId) </span><span style="color: #008000;">//</span><span style="color: #008000;">该单位dimen里的属性，可以设置为dp值</span></pre>
</div>
<p><strong>　　6.设置缩放(Scale)</strong></p>
</div>
<div>　　　　只有当原始图片的尺寸大于我们指定的尺寸时，resize才起作用</div>
<div>
<div class="cnblogs_code">
<pre>.resize(<span style="color: #800080;">4000</span>,<span style="color: #800080;">2000</span><span style="color: #000000;">)
.onlyScaleDown()</span></pre>
</div>
<p><strong>　　7.设置裁剪(Crop)</strong></p>
</div>
<div>&nbsp;　　　　<strong>7.1 centerCrop()</strong>充满ImageView 的边界，居中裁剪。ImageView 的ScaleType 也有这个属性。<br>&nbsp;
<div class="cnblogs_code">
<pre>.centerCrop()        </pre>
</div>
<p>　　　　<strong>7.2</strong> 上面的centerCrop是可能看不到全部图片的，如果你想让View将图片展示完全，可以用<strong>centerInside</strong>，但是如果图片尺寸小于View尺寸的话，是不能充满View边界的。</p>
<div class="cnblogs_code">
<pre> .centerInside()</pre>
</div>
<p>　　　　<strong>7.3 fit</strong> 它会自动测量我们的View的大小，然后内部调用reszie方法把图片裁剪到View的大小，这就帮我们做了计算size和调用resize。</p>
<div class="cnblogs_code">
<pre>  .fit()</pre>
</div>
<p>&nbsp;使用fit 还是会出现拉伸扭曲的情况，因此最好配合前面的centerCrop使用</p>
</div>
<div>
<div class="cnblogs_code">
<pre><span style="color: #000000;">.fit()
.centerCrop()</span></pre>
</div>
<p>　　<strong>注意</strong>：</p>
</div>
<div>　　&nbsp;　　1，fit 只对ImageView 有效。<br>&nbsp;　　　　2，使用fit时，ImageView 宽和高不能为wrap_content,很好理解，因为它要测量宽高。<br>&nbsp;<br>　　<strong>8.图片旋转rotate(int degree)，该方法它是默认以（0，0）点旋转。</strong><br>
<div class="cnblogs_code">
<pre>.rotate(<span style="color: #800080;">180</span><span style="color: #000000;">)
 </span><span style="color: #008000;">//</span><span style="color: #008000;">rotate(float degrees, float pivotX, float pivotY) 以(pivotX, pivotY)为原点旋转</span>
 .rotate(<span style="color: #800080;">180</span>,<span style="color: #800080;">100</span>,<span style="color: #800080;">100</span>)</pre>
</div>
<p><strong>&nbsp;9.转换器Transformation 添加依赖时候添加了Picasso Transformation的依赖。</strong></p>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">jp.wasabeef:picasso-transformations:2.1.0</span><span style="color: #800000;">'</span></pre>
</div>
<p>&nbsp;　　Transformation 这就是Picasso的一个非常强大的功能了，它允许你在load图片 -&gt; into ImageView 中间这个过成对图片做一系列的变换。比如你要做图片高斯模糊、添加圆角、做度灰处理、圆形图片等等都可以通过Transformation来完成。</p>
&nbsp;具体使用可以自己继续探索，功能很多，</div>
</div>