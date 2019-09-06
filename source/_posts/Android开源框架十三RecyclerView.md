---
title: Android 开源框架 ( 十三 ) RecyclerView
date: 2018-08-05 21:19:57
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/recyclerview.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　RecyclerView是Android 5.0推出的。<br>　　RecyclerView是support:recyclerview-v7中提供的控件，最低兼容到android 3.0版本。可以通过导入support-v7对其进行使用。<br>　　RecyclerView已经标准化ViewHolder，我们自定义的ViewHoler需要继承 RecyclerView.ViewHolder，然后在构造方法中初始化控件，后面会有具体介绍。通过设置不同的LayoutManager，以及结合ItemDecoration , ItemAnimator，ItemTouchHelper，可以实现非常炫酷的效果。</p>
<h2>一.布局&nbsp;</h2>
<div class="cnblogs_code">
<pre>  &lt;<span style="color: #000000;">android.support.v7.widget.RecyclerView
        android:id</span>=<span style="color: #800000;">"</span><span style="color: #800000;">@+id/recyclerView</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_width</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span><span style="color: #000000;">
        android:layout_height</span>=<span style="color: #800000;">"</span><span style="color: #800000;">match_parent</span><span style="color: #800000;">"</span>/&gt;  </pre>
</div>
<p>&nbsp;</p>
<h2>二.基本使用&nbsp;</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">设置RecyclerView的适配器</span>
adapter = <span style="color: #0000ff;">new</span> MyRecyclerViewAdapter(RecyclerViewActivity.<span style="color: #0000ff;">this</span><span style="color: #000000;">,datas);
recyclerview.setAdapter(adapter);
</span><span style="color: #008000;">//</span><span style="color: #008000;">设置添加或删除item时的动画，这里使用默认动画</span>
mRecyclerView.setItemAnimator(<span style="color: #0000ff;">new</span><span style="color: #000000;"> DefaultItemAnimator());
</span><span style="color: #008000;">//</span><span style="color: #008000;">添加RecyclerView的分割线</span>
recyclerview.addItemDecoration(<span style="color: #0000ff;">new</span> DividerListItemDecoration(RecyclerViewActivity.<span style="color: #0000ff;">this</span><span style="color: #000000;">,DividerListItemDecoration.VERTICAL_LIST));        
</span><span style="color: #008000;">//</span><span style="color: #008000;">LayoutManager</span>
recyclerview.setLayoutManager(<span style="color: #0000ff;">new</span> LinearLayoutManager(RecyclerViewActivity.<span style="color: #0000ff;">this</span>, LinearLayoutManager.VERTICAL, <span style="color: #0000ff;">false</span>));</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span> MyRecyclerViewAdapter extends RecyclerView.Adapter&lt;MyRecyclerViewAdapter.ViewHolder&gt;<span style="color: #000000;"> {

    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> final Context context;
    </span><span style="color: #0000ff;">private</span>  ArrayList&lt;String&gt;<span style="color: #000000;"> datas;

    </span><span style="color: #0000ff;">public</span> MyRecyclerViewAdapter(Context context, ArrayList&lt;String&gt;<span style="color: #000000;"> datas) {
        </span><span style="color: #0000ff;">this</span>.context =<span style="color: #000000;"> context;
        </span><span style="color: #0000ff;">this</span>.datas =<span style="color: #000000;"> datas;
    }

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * 相当于getView方法中创建View和ViewHolder
     * @param parent
     * @param viewType
     * @return
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span> ViewHolder onCreateViewHolder(ViewGroup parent, <span style="color: #0000ff;">int</span><span style="color: #000000;"> viewType) {
        View itemView </span>= View.inflate(context, R.layout.item_recyclerview,<span style="color: #0000ff;">null</span><span style="color: #000000;">);
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> ViewHolder(itemView);
    }

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*相当于于getView绑定数据部分的代码
     * 数据和View绑定
     * @param holder
     * @param position
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onBindViewHolder(ViewHolder holder, <span style="color: #0000ff;">int</span><span style="color: #000000;"> position) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;">根据位置得到对应的数据</span>
        String  data  = datas.<span style="color: #0000ff;">get</span><span style="color: #000000;">(position);
        holder.tv_title.setText(data);

    }

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * 得到总条数
     * @return
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">int</span><span style="color: #000000;"> getItemCount() {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> datas.size();
    }


    </span><span style="color: #0000ff;">class</span><span style="color: #000000;"> ViewHolder extends RecyclerView.ViewHolder {

        </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> ImageView iv_icon;
        </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> TextView tv_title;

        </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> ViewHolder(View itemView) {
            super(itemView);
            iv_icon </span>=<span style="color: #000000;"> (ImageView) itemView.findViewById(R.id.iv_icon);
            tv_title </span>=<span style="color: #000000;"> (TextView) itemView.findViewById(R.id.tv_title);

        }
    }

}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h2>三.三种显示方式&nbsp;</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">设置List类型效果</span>
recyclerview.setLayoutManager(<span style="color: #0000ff;">new</span> LinearLayoutManager(RecyclerViewActivity.<span style="color: #0000ff;">this</span>,LinearLayoutManager.VERTICAL,<span style="color: #0000ff;">false</span><span style="color: #000000;">));

</span><span style="color: #008000;">//</span><span style="color: #008000;">设置Grid类型效果</span>
recyclerview.setLayoutManager(<span style="color: #0000ff;">new</span> GridLayoutManager(RecyclerViewActivity.<span style="color: #0000ff;">this</span>, <span style="color: #800080;">2</span>, GridLayoutManager.VERTICAL, <span style="color: #0000ff;">false</span><span style="color: #000000;">));

</span><span style="color: #008000;">//</span><span style="color: #008000;">设置瀑布流类型效果</span>
recyclerview.setLayoutManager(<span style="color: #0000ff;">new</span> StaggeredGridLayoutManager(<span style="color: #800080;">3</span>,StaggeredGridLayoutManager.VERTICAL));</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h2>四.分割线</h2>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">添加RecyclerView的分割线</span>
recyclerview.addItemDecoration(<span style="color: #0000ff;">new</span> DividerListItemDecoration(RecyclerViewActivity.<span style="color: #0000ff;">this</span>,DividerListItemDecoration.VERTICAL_LIST));</pre>
</div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Canvas;
import android.graphics.Rect;
import android.graphics.drawable.Drawable;
import android.support.v7.widget.LinearLayoutManager;
import android.support.v7.widget.RecyclerView;
import android.view.View;

</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> DividerListItemDecoration extends RecyclerView.ItemDecoration {
    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> final <span style="color: #0000ff;">int</span>[] ATTRS = <span style="color: #0000ff;">new</span> <span style="color: #0000ff;">int</span><span style="color: #000000;">[]{
            android.R.attr.listDivider
    };

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> final <span style="color: #0000ff;">int</span> HORIZONTAL_LIST =<span style="color: #000000;"> LinearLayoutManager.HORIZONTAL;

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> final <span style="color: #0000ff;">int</span> VERTICAL_LIST =<span style="color: #000000;"> LinearLayoutManager.VERTICAL;

    </span><span style="color: #0000ff;">private</span><span style="color: #000000;"> Drawable mDivider;

    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">int</span><span style="color: #000000;"> mOrientation;

    </span><span style="color: #0000ff;">public</span> DividerListItemDecoration(Context context, <span style="color: #0000ff;">int</span><span style="color: #000000;"> orientation) {
        final TypedArray a </span>=<span style="color: #000000;"> context.obtainStyledAttributes(ATTRS);
        mDivider </span>= a.getDrawable(<span style="color: #800080;">0</span><span style="color: #000000;">);
        a.recycle();
        setOrientation(orientation);
    }

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> setOrientation(<span style="color: #0000ff;">int</span><span style="color: #000000;"> orientation) {
        </span><span style="color: #0000ff;">if</span> (orientation != HORIZONTAL_LIST &amp;&amp; orientation !=<span style="color: #000000;"> VERTICAL_LIST) {
            </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalArgumentException(<span style="color: #800000;">"</span><span style="color: #800000;">invalid orientation</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        }
        mOrientation </span>=<span style="color: #000000;"> orientation;
    }

    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onDraw(Canvas c, RecyclerView parent) {
</span><span style="color: #008000;">//</span><span style="color: #008000;">        Log.e("recyclerview - itemdecoration", "onDraw()");</span>

        <span style="color: #0000ff;">if</span> (mOrientation ==<span style="color: #000000;"> VERTICAL_LIST) {
            drawVertical(c, parent);
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            drawHorizontal(c, parent);
        }

    }


    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> drawVertical(Canvas c, RecyclerView parent) {
        final </span><span style="color: #0000ff;">int</span> left =<span style="color: #000000;"> parent.getPaddingLeft();
        final </span><span style="color: #0000ff;">int</span> right = parent.getWidth() -<span style="color: #000000;"> parent.getPaddingRight();

        final </span><span style="color: #0000ff;">int</span> childCount =<span style="color: #000000;"> parent.getChildCount();
        </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; childCount; i++<span style="color: #000000;">) {
            final View child </span>=<span style="color: #000000;"> parent.getChildAt(i);
            android.support.v7.widget.RecyclerView v </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> android.support.v7.widget.RecyclerView(parent.getContext());
            final RecyclerView.LayoutParams </span><span style="color: #0000ff;">params</span> =<span style="color: #000000;"> (RecyclerView.LayoutParams) child
                    .getLayoutParams();
            final </span><span style="color: #0000ff;">int</span> top = child.getBottom() + <span style="color: #0000ff;">params</span><span style="color: #000000;">.bottomMargin;
            final </span><span style="color: #0000ff;">int</span> bottom = top +<span style="color: #000000;"> mDivider.getIntrinsicHeight();
            mDivider.setBounds(left, top, right, bottom);
            mDivider.draw(c);
        }
    }

    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> drawHorizontal(Canvas c, RecyclerView parent) {
        final </span><span style="color: #0000ff;">int</span> top =<span style="color: #000000;"> parent.getPaddingTop();
        final </span><span style="color: #0000ff;">int</span> bottom = parent.getHeight() -<span style="color: #000000;"> parent.getPaddingBottom();

        final </span><span style="color: #0000ff;">int</span> childCount =<span style="color: #000000;"> parent.getChildCount();
        </span><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; childCount; i++<span style="color: #000000;">) {
            final View child </span>=<span style="color: #000000;"> parent.getChildAt(i);
            final RecyclerView.LayoutParams </span><span style="color: #0000ff;">params</span> =<span style="color: #000000;"> (RecyclerView.LayoutParams) child
                    .getLayoutParams();
            final </span><span style="color: #0000ff;">int</span> left = child.getRight() + <span style="color: #0000ff;">params</span><span style="color: #000000;">.rightMargin;
            final </span><span style="color: #0000ff;">int</span> right = left +<span style="color: #000000;"> mDivider.getIntrinsicHeight();
            mDivider.setBounds(left, top, right, bottom);
            mDivider.draw(c);
        }
    }

    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> getItemOffsets(Rect outRect, <span style="color: #0000ff;">int</span><span style="color: #000000;"> itemPosition, RecyclerView parent) {
        </span><span style="color: #0000ff;">if</span> (mOrientation ==<span style="color: #000000;"> VERTICAL_LIST) {
            outRect.</span><span style="color: #0000ff;">set</span>(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">, mDivider.getIntrinsicHeight());
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            outRect.</span><span style="color: #0000ff;">set</span>(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, mDivider.getIntrinsicWidth(), <span style="color: #800080;">0</span><span style="color: #000000;">);
        }
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<p>　　RecyclerView 和 ListView 都可以加头和尾。 在后面列表的上拉加载，下拉刷新控件中再讲。</p>
</div>