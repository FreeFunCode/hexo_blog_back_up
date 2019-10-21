---
title: ViewPager相关使用
date: 2018-07-21 23:59:41
tags: ViewPager
categories: 
- Android
- 知识点
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/viewpager.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.ViewPager+Fragment 预加载</h2>
<p>&nbsp;ViewPager的预加载，是指ViewPager的内部加载数据机制，它会默认至少预加载一个相邻的ViewPager内的Fragment页数据。<br>&nbsp;<br>&nbsp;如果设置 viewpager.setOffscreenPageLimit(0); 会发现没有效果。<br>&nbsp;可以查看ViewPager.java源码我们知道，ViewPager默认必须预加载一个相邻页面的数据。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> ViewPager extends ViewGroup {
    ...
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> final <span style="color: #0000ff;">int</span> DEFAULT_OFFSCREEN_PAGES = <span style="color: #800080;">1</span><span style="color: #000000;">;
    ...
    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * Set the number of pages that should be retained to either side of the
     * current page in the view hierarchy in an idle state. Pages beyond this
     * limit will be recreated from the adapter when needed.
     *
     * &lt;p&gt;This is offered as an optimization. If you know in advance the number
     * of pages you will need to support or have lazy-loading mechanisms in place
     * on your pages, tweaking this setting can have benefits in perceived smoothness
     * of paging animations and interaction. If you have a small number of pages (3-4)
     * that you can keep active all at once, less time will be spent in layout for
     * newly created view subtrees as the user pages back and forth.&lt;/p&gt;
     *
     * &lt;p&gt;You should keep this limit low, especially if your pages have complex layouts.
     * This setting defaults to 1.&lt;/p&gt;
     *
     * @param limit How many pages will be kept offscreen in an idle state.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> setOffscreenPageLimit(<span style="color: #0000ff;">int</span><span style="color: #000000;"> limit) {
        </span><span style="color: #0000ff;">if</span> (limit &lt;<span style="color: #000000;"> DEFAULT_OFFSCREEN_PAGES) {
            Log.w(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">Requested offscreen page limit </span><span style="color: #800000;">"</span> + limit + <span style="color: #800000;">"</span><span style="color: #800000;"> too small; defaulting to </span><span style="color: #800000;">"</span> +<span style="color: #000000;">
                    DEFAULT_OFFSCREEN_PAGES);
            limit </span>=<span style="color: #000000;"> DEFAULT_OFFSCREEN_PAGES;
        }
        </span><span style="color: #0000ff;">if</span> (limit !=<span style="color: #000000;"> mOffscreenPageLimit) {
            mOffscreenPageLimit </span>=<span style="color: #000000;"> limit;
            populate();
        }
    }
    ...
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div>&nbsp; &nbsp; 如果确实有时候非要相邻的Fragment页面需要实时显示的时候就要刷新数据，我们可以使用Fragment里的一个方法。<br>&nbsp; &nbsp; 重写 public void setUserVisibleHint(boolean isVisibleToUser) {...} 来处理请求刷新数据。</div>
<div>&nbsp; &nbsp; 当然也有很多其他方法来解决页面立即刷新问题，比如：利用EventBus监听事件刷新，或者FragmentManager来控制Fragment响应事件的触发更新等等。</div>
<div>&nbsp;</div>
<div>
<div>
<h2>二.FragmentStatePagerAdapter 和 FragmentPagerAdapter</h2>
<br>&nbsp;&nbsp;&nbsp;&nbsp; ViewPager 经常和 Fragment 一起使用，并且提供了专门的 FragmentPagerAdapter 和 FragmentStatePagerAdapter 类供 Fragment 中的 ViewPager 使用。</div>
<div>
<h4>&nbsp;1.FragmentStatePagerAdapter</h4>
Implementation of PagerAdapter that uses a Fragment to manage each page. This class also handles saving and restoring of fragment's state. <br>&nbsp;&nbsp;&nbsp; This version of the pager is more useful when there are a large number of pages, working more like a list view. <br>&nbsp;When pages are not visible to the user, their entire fragment may be destroyed, only keeping the saved state of that fragment.<br>&nbsp;This allows the pager to hold on to much less memory associated with each visited page as compared to FragmentPagerAdapter at the cost of potentially more overhead when switching between pages. <br>&nbsp;&nbsp;&nbsp; When using FragmentPagerAdapter the host ViewPager must have a valid ID set.<br>&nbsp;&nbsp;&nbsp; Subclasses only need to implement getItem(int) and getCount() to have a working adapter. </div>
<div>&nbsp;</div>
<div>&nbsp; &nbsp;&nbsp; 从FragmentStatePagerAdapter的Api可知。<strong>当ViewPager管理的页面有大量数据时候，也就是如果viewpager管理的页面是一个ListView时候，推荐使用FragmentStatePagerAdapter</strong>。这是因为FragmentStatePagerAdapter在页面不可见的时候，会销毁这个Fragment。所以FragmentStatePagerAdapter比FragmentPagerAdapter占用内存更小。<br>
<h4> 2.FragmentPagerAdapter</h4>
&nbsp;Implementation of PagerAdapter that represents each page as a Fragment that is persistently kept in the fragment manager as long as the user can return to the page. <br>&nbsp;This version of the pager is best for use when there are a handful of typically more static fragments to be paged through, such as a set of tabs. The fragment of each page the user visits will be kept in memory, <br>&nbsp;though its view hierarchy may be destroyed when not visible. This can result in using a significant amount of memory since fragment instances can hold on to an arbitrary amount of state. <br>&nbsp;For larger sets of pages, consider FragmentStatePagerAdapter. <br>&nbsp;When using FragmentPagerAdapter the host ViewPager must have a valid ID set.<br>&nbsp;Subclasses only need to implement getItem(int) and getCount() to have a working adapter. <br>&nbsp;<br>&nbsp;从FragmentPagerAdapter的Api也同样了解到，当页面有大量数据加载的时候推荐使用FragmentStatePagerAdapter，FragmentPagerAdapter 创建的 fragment 永远不会被销毁，一般用于<strong>只有少量固定的 fragment 时，可以选择 FragmentPagerAdapter</strong>。<br>&nbsp;<br><br>&nbsp;最后说一点，在内存优化上，<strong>RecyclerView要比ViewPager优秀</strong>些。推荐大家优先考虑使用RecyclerView 实现。</div>
</div>
</div>