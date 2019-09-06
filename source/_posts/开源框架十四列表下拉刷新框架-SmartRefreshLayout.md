---
layout: android
title: Android 开源框架 ( 十四 ) 列表下拉刷新框架 --- SmartRefreshLayout
date: 2018-08-07 20:23
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/SmartRefresh.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　结合上一篇RecyclerView,介绍一个针对列表的下拉和上拉刷新,加载的框架:SmartRefreshLayout.</p>
<p>　　　　<a id="post_title_link_9427210" href="https://www.cnblogs.com/bugzone/p/recyclerview.html" target="_blank">Android 开源框架 ( 十三 ) RecyclerView</a></p>
<p>　　可实现自定义Header/Footer</p>
<h3>　　一.添加依赖</h3>
<div class="cnblogs_code">
<pre>    compile <span style="color: #800000;">'</span><span style="color: #800000;">com.scwang.smartrefresh:SmartRefreshLayout:1.0.4-7</span><span style="color: #800000;">'</span><span style="color: #000000;">
    compile </span><span style="color: #800000;">'</span><span style="color: #800000;">com.scwang.smartrefresh:SmartRefreshHeader:1.0.4-7</span><span style="color: #800000;">'</span></pre>
</div>
<h3>　　二. 布局文件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>&lt;<span style="color: #000000;">com.scwang.smartrefresh.layout.SmartRefreshLayout
        android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/refreshLayout</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
        app:srlEnablePreviewInEditMode</span>=<span style="color: #800000;">"</span><span style="color: #800000;">true</span><span style="color: #800000;">"</span>&gt;
        &lt;!--srlAccentColor srlPrimaryColor 将会改变 Header 和 Footer 的主题颜色--&gt;
        &lt;!--srlEnablePreviewInEditMode 可以开启和关闭预览功能--&gt;
        &lt;<span style="color: #000000;">com.scwang.smartrefresh.layout.header.ClassicsHeader
            android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
            android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">wrap_content</span><span style="color: #800000;">"</span><span style="color: #000000;">
            app:srlAccentColor</span>=<span style="color: #800000;">"</span><span style="color: #800000;">#000</span><span style="color: #800000;">"</span><span style="color: #000000;">
            app:srlPrimaryColor</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@android:color/white</span><span style="color: #800000;">"</span> /&gt;


        &lt;<span style="color: #000000;">android.support.v7.widget.RecyclerView
            android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/recycler_view</span><span style="color: #800000;">"</span><span style="color: #000000;">
            android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
            android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span> /&gt;

        &lt;<span style="color: #000000;">com.scwang.smartrefresh.layout.footer.ClassicsFooter
            android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
            android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">wrap_content</span><span style="color: #800000;">"</span><span style="color: #000000;">
            app:srlAccentColor</span>=<span style="color: #800000;">"</span><span style="color: #800000;">#000</span><span style="color: #800000;">"</span><span style="color: #000000;">
            app:srlPrimaryColor</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@android:color/white</span><span style="color: #800000;">"</span> /&gt;
    &lt;/com.scwang.smartrefresh.layout.SmartRefreshLayout&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<pre><span>app:srlEnablePreviewInEditMode="true" 开启了预览功能<br>　　<img src="https://images2018.cnblogs.com/blog/612293/201808/612293-20180806195400466-647305574.jpg" alt=""><br><br></span></pre>
<h3>　　三. 使用-上拉,下拉</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>refreshLayout.setOnRefreshListener(<span style="color: #0000ff;">new</span><span style="color: #000000;"> OnRefreshListener() {
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onRefresh(RefreshLayout refreshlayout) {
            pageNumber </span>= <span style="color: #800080;">1</span><span style="color: #000000;">;
            </span><span style="color: #008000;">//</span><span style="color: #008000;">刷新数据</span>
<span style="color: #000000;">        }
    });

    refreshLayout.setOnLoadmoreListener(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> OnLoadmoreListener() {
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onLoadmore(RefreshLayout refreshlayout) {
            </span>++<span style="color: #000000;"> pageNumber;
            </span><span style="color: #008000;">//</span><span style="color: #008000;">加载数据</span>
<span style="color: #000000;">        }
    });
    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　四.关闭动画</h3>
<div class="cnblogs_code">
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;">关闭刷新</span>
    <span style="color: #0000ff;">if</span> (refreshLayout != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
        refreshLayout.finishRefresh(</span><span style="color: #800080;">200</span><span style="color: #000000;">);
        refreshLayout.finishLoadmore(</span><span style="color: #800080;">200</span><span style="color: #000000;">);
    }</span></pre>
</div>
<h3>　　五.拓展- 参数属性介绍</h3>
<h4>　　　　　　　　　　　　　　　　　　　　　　　　属性 Attributes</h4>
<table style="height: 395px; width: 968px;">
<thead>
<tr><th><span style="font-size: 15px;">名称-name</span></th><th><span style="font-size: 15px;">格式-format</span></th><th><span style="font-size: 15px;">描述-description</span></th></tr>
</thead>
<tbody>
<tr>
<td><span style="font-size: 15px;">srlPrimaryColor</span></td>
<td><span style="font-size: 15px;">color</span></td>
<td><span style="font-size: 15px;">主题颜色</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlAccentColor</span></td>
<td><span style="font-size: 15px;">color</span></td>
<td><span style="font-size: 15px;">强调颜色</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlReboundDuration</span></td>
<td><span style="font-size: 15px;">integer</span></td>
<td><span style="font-size: 15px;">释放后回弹动画时长</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlHeaderHeight</span></td>
<td><span style="font-size: 15px;">dimension</span></td>
<td><span style="font-size: 15px;">Header的标准高度</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlFooterHeight</span></td>
<td><span style="font-size: 15px;">dimension</span></td>
<td><span style="font-size: 15px;">Footer的标准高度</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">显示拖动高度/真实拖动高度（默认0.5，阻尼效果）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlHeaderMaxDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">Header最大拖动高度/Header标准高度（默认2，要求&gt;=1）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlFooterMaxDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">Footer最大拖动高度/Footer标准高度（默认2，要求&gt;=1）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlEnableRefresh</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否开启下拉刷新功能（默认true）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlEnableLoadmore</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否开启加上拉加载功能（默认true）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlEnableHeaderTranslationContent</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">拖动Header的时候是否同时拖动内容（默认true）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlEnableFooterTranslationContent</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">拖动Footer的时候是否同时拖动内容（默认true）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlEnablePreviewInEditMode</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否在编辑模式时显示预览效果（默认true）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlDisableContentWhenRefresh</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否在刷新的时候禁止内容的一切手势操作（默认false）</span></td>
</tr>
<tr>
<td><span style="font-size: 15px;">srlDisableContentWhenLoading</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否在加载的时候禁止内容的一切手势操作（默认false）</span></td>
</tr>
</tbody>
</table>
<p><br><br></p>
<p>　　　　　　　　　　　　　　　　　　　　　　　　</p>
<h4 id="articleHeader8">　　　　　　　　　　　　　　　　　　　　　　　　　　方法 Method</h4>
<table style="height: 990px; width: 958px;">
<thead>
<tr><th><span style="font-size: 15px;">名称-name</span></th><th><span style="font-size: 15px;">格式-format</span></th><th><span style="font-size: 15px;">描述-description</span></th></tr>

</thead>
<tbody>
<tr>
<td><span style="font-size: 15px;">setPrimaryColors</span></td>
<td><span style="font-size: 15px;">colors</span></td>
<td><span style="font-size: 15px;">主题\强调颜色</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setPrimaryColorsId</span></td>
<td><span style="font-size: 15px;">colors</span></td>
<td><span style="font-size: 15px;">主题\强调颜色资源Id</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setReboundDuration</span></td>
<td><span style="font-size: 15px;">integer</span></td>
<td><span style="font-size: 15px;">释放后回弹动画时长</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setHeaderHeight</span></td>
<td><span style="font-size: 15px;">dimension</span></td>
<td><span style="font-size: 15px;">Header的标准高度（px/dp 两个版本）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setFooterHeight</span></td>
<td><span style="font-size: 15px;">dimension</span></td>
<td><span style="font-size: 15px;">Footer的标准高度（px/dp 两个版本）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">显示拖动高度/真实拖动高度（默认0.5，阻尼效果）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setHeaderMaxDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">Header最大拖动高度/Header标准高度（默认2，要求&gt;=1）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setFooterMaxDragRate</span></td>
<td><span style="font-size: 15px;">float</span></td>
<td><span style="font-size: 15px;">Footer最大拖动高度/Footer标准高度（默认2，要求&gt;=1）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setEnableRefresh</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否开启下拉刷新功能（默认true）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setEnableLoadmore</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否开启加上拉加载功能（默认true）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setEnableHeaderTranslationContent</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">拖动Header的时候是否同时拖动内容（默认true）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setEnableFooterTranslationContent</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">拖动Footer的时候是否同时拖动内容（默认true）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setEnableAutoLoadmore</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否监听列表滚动到底部时触发加载事件</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setDisableContentWhenRefresh</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否在刷新的时候禁止内容的一切手势操作（默认false）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setDisableContentWhenLoading</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否在加载的时候禁止内容的一切手势操作（默认false）</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setReboundInterpolator</span></td>
<td><span style="font-size: 15px;">Interpolator</span></td>
<td><span style="font-size: 15px;">设置回弹动画的插值器</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setRefreshHeader</span></td>
<td><span style="font-size: 15px;">RefreshHeader</span></td>
<td><span style="font-size: 15px;">设置指定的Header</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setRefreshFooter</span></td>
<td><span style="font-size: 15px;">RefreshFooter</span></td>
<td><span style="font-size: 15px;">设置指定的Footer</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setOnRefreshListener</span></td>
<td><span style="font-size: 15px;">OnRefreshListener</span></td>
<td><span style="font-size: 15px;">设置刷新监听器</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setOnLoadmoreListener</span></td>
<td><span style="font-size: 15px;">OnLoadmoreListener</span></td>
<td><span style="font-size: 15px;">设置加载监听器</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setOnRefreshLoadmoreListener</span></td>
<td><span style="font-size: 15px;">OnRefreshLoadmoreListener</span></td>
<td><span style="font-size: 15px;">同时设置上面两个监听器</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setOnMultiPurposeListener</span></td>
<td><span style="font-size: 15px;">OnMultiPurposeListener</span></td>
<td><span style="font-size: 15px;">设置多功能监听器</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">setLoadmoreFinished</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">设置全部数据加载完成，之后不会触发加载事件</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">finishRefresh</span></td>
<td><span style="font-size: 15px;">(int delayed)</span></td>
<td><span style="font-size: 15px;">完成刷新，结束刷新动画</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">finishLoadmore</span></td>
<td><span style="font-size: 15px;">(int delayed)</span></td>
<td><span style="font-size: 15px;">完成加载，结束加载动画</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">getRefreshHeader</span></td>
<td><span style="font-size: 15px;">RefreshHeader</span></td>
<td><span style="font-size: 15px;">获取Header</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">getRefreshFooter</span></td>
<td><span style="font-size: 15px;">RefreshFooter</span></td>
<td><span style="font-size: 15px;">获取Footer</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">getState</span></td>
<td><span style="font-size: 15px;">RefreshState</span></td>
<td><span style="font-size: 15px;">获取当前状态</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">isRefreshing</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否正在刷新</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">isLoading</span></td>
<td><span style="font-size: 15px;">boolean</span></td>
<td><span style="font-size: 15px;">是否正在加载</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">autoRefresh</span></td>
<td><span style="font-size: 15px;">(int delayed)</span></td>
<td><span style="font-size: 15px;">触发自动刷新</span></td>

</tr>
<tr>
<td><span style="font-size: 15px;">autoLoadmore</span></td>
<td><span style="font-size: 15px;">(int delayed)</span></td>
<td><span style="font-size: 15px;">触发自动加载</span></td>

</tr>

</tbody>

</table>
<pre><span><br>　　<span style="font-size: 14px;">参数属性引用自: </span></span><span style="font-size: 14px;"><a href="https://blog.csdn.net/lknlll/article/details/77988978?locationNum=2&amp;fps=1" target="_blank">SmartRefreshLayout---“智能”下拉刷新布局</a> 感谢原作者!</span></pre>
<pre><span>&nbsp;</span></pre>
</div>