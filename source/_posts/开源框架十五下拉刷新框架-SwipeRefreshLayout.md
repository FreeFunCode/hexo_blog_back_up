---
layout: android
title: Android 开源框架 ( 十五 ) 下拉刷新框架 --- SwipeRefreshLayout
date: 2018-08-07 20:28:54
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/SwipeRefreshLayout.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　SwipeRefreshLayout是Google官方更新的一个Widget,可以实现下拉刷新的效果。完整包名:android.support.v4.widget.SwipeRefreshLayout .</p>
<h3>一. 布局</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>&lt;<span style="color: #000000;">android.support.v4.widget.SwipeRefreshLayout
        android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/srl</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">wrap_content</span><span style="color: #800000;">"</span>&gt;

        .............

&lt;/android.support.v4.widget.SwipeRefreshLayout&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>二. Activity继承SwipeRefreshLayout刷新监听</h3>
<div class="cnblogs_code">
<pre>implements SwipeRefreshLayout.OnRefreshListener</pre>
</div>
<h3>三.添加监听</h3>
<div class="cnblogs_code">
<pre>srl.setOnRefreshListener(<span style="color: #0000ff;">this</span>);</pre>
</div>
<h3>&nbsp;四.改变刷新图标颜色</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">CommonUtils.setColorSchemeResources(srl);
    
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> CommonUtils {
    
        </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
         * 统一维护，方便修改
         *
         * @param sr_layout 下拉控件
         </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> setColorSchemeResources(SwipeRefreshLayout sr_layout) {
            </span><span style="color: #0000ff;">if</span> (sr_layout != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                sr_layout.setColorSchemeResources(R.color.refresh1, R.color.refresh2, R.color
                        .refresh3, R
                        .color.refresh4);
            }
        }
    
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>五. 重写SwipeRefreshLayout刷新监听事件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;">刷新</span>
<span style="color: #000000;">    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onRefresh() {
        isPullToRefreshRequest </span>= <span style="color: #0000ff;">true</span><span style="color: #000000;">;
        </span><span style="color: #008000;">//</span><span style="color: #008000;">刷新数据</span>
<span style="color: #000000;">    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;">加载动画结束</span>
<span style="color: #000000;">    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onAllRequestsFinished() {
        super.onAllRequestsFinished();
        srl.setRefreshing(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>六. 效果</h3>
<p><img src="https://images2018.cnblogs.com/blog/612293/201808/612293-20180806202511108-3463627.jpg" alt=""></p>
</div>