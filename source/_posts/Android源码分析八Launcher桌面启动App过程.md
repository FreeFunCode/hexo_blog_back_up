---
title: Android 源码分析（八） Launcher 桌面启动App过程
date: 2018-07-07 20:13:12
tags: 安卓源码
categories: 
- Android
- 安卓源码
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/Launcher_app.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.前言:</h2>
<p>&nbsp;&nbsp;&nbsp; init进程 –&gt; Zygote进程 –&gt; SystemServer进程 –&gt; Launcher桌面程序 -&gt; 我们的App应用<br><br>&nbsp;&nbsp;&nbsp; init进程：linux的根进程，android系统是基于linux系统的，因此可以算作是整个android操作系统的第一个进程；<br><br>&nbsp;&nbsp;&nbsp; Zygote进程：android系统的根进程，主要作用：可以作用Zygote进程fork出SystemServer进程和各种应用进程；<br><br>&nbsp;&nbsp;&nbsp; SystemService进程：主要是在这个进程中启动系统的各项服务，比如ActivityManagerService，PackageManagerService，WindowManagerService服务等等；<br>&nbsp;&nbsp; &nbsp;<br>&nbsp;&nbsp; &nbsp;Launcher桌面程序:就是我们平时看到的桌面程序，它其实也是一个android应用程序，只不过这个应用程序是系统默认第一个启动的应用程序.<br>&nbsp;&nbsp;&nbsp; </p>
<h2>&nbsp; 二. Launcher 桌面启动App过程分析</h2>
<p>Launcher应用程序在启动过程中会通过PackageManagerService服务请求查询系统所有的已安装应用的包名，图标和应用名称等信息，然后填充到Launcher中的Adapter中.<br>这样点击某一项应用图标的时候就可以根据该图标的包名和启动Activity的类名初始化Intent对象，然后调用startActivity(Intent)启动相关的应用程序了。<br>其实android中应用进程可以通过许多方式启动，比如启动一个Activity，启动一个Service，启动一个ContentProvider或者是一个BroadcastReceiver，也就是说我们可以通过启动四大组件的方式启动应用进程，在应用进程没有启动的时候，如果我们通过启动这些组件，这时候系统会判断当前这些组件所需要的应用进程是否已经启动，若没有的话，则会启动应用进程。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">LauncherActivity.java</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">abstract</span> <span style="color: #0000ff;">class</span> LauncherActivity <span style="color: #0000ff;">extends</span><span style="color: #000000;"> ListActivity {
    ......
    @Override
    </span><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span> onListItemClick(ListView l, View v, <span style="color: #0000ff;">int</span> position, <span style="color: #0000ff;">long</span><span style="color: #000000;"> id) {
        Intent intent </span>=<span style="color: #000000;"> intentForPosition(position);
        startActivity(intent);
    }
    ......

    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> startActivity(Intent intent) {
        </span><span style="color: #0000ff;">this</span>.startActivity(intent, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
    }
    ......
     @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> startActivity(Intent intent, @Nullable Bundle options) {
        </span><span style="color: #0000ff;">if</span> (options != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            startActivityForResult(intent, </span>-1<span style="color: #000000;">, options);
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> Note we want to go through this call for compatibility with
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> applications that may have overridden the method.</span>
            startActivityForResult(intent, -1<span style="color: #000000;">);
        }
    }
    ......
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Adapter which shows the set of activities that can be performed for a given intent.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">class</span> ActivityAdapter <span style="color: #0000ff;">extends</span> BaseAdapter <span style="color: #0000ff;">implements</span><span style="color: #000000;"> Filterable {
        ......
        </span><span style="color: #0000ff;">public</span> View getView(<span style="color: #0000ff;">int</span><span style="color: #000000;"> position, View convertView, ViewGroup parent) {
            View view;
            </span><span style="color: #0000ff;">if</span> (convertView == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                view </span>=<span style="color: #000000;"> mInflater.inflate(
                        com.android.internal.R.layout.activity_list_item_2, parent, </span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                view </span>=<span style="color: #000000;"> convertView;
            }
            bindView(view, mActivitiesList.get(position));
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> view;
        }

        </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> bindView(View view, ListItem item) {
            TextView text </span>=<span style="color: #000000;"> (TextView) view;
            text.setText(item.label);
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (mShowIcons) {
                </span><span style="color: #0000ff;">if</span> (item.icon == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    item.icon </span>=<span style="color: #000000;"> mIconResizer.createIconThumbnail(item.resolveInfo.loadIcon(getPackageManager()));
                }
                text.setCompoundDrawablesWithIntrinsicBounds(item.icon, </span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
            }
        }
        ......
    }
    
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">activity_list.xml Launcher 桌面就是一个列表控件，来存放所有应用的图标和名称</span>
&lt;FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"<span style="color: #000000;">
    android:layout_width</span>="match_parent"<span style="color: #000000;">
    android:layout_height</span>="match_parent"
    &gt;

    &lt;<span style="color: #000000;">ListView
        android:id</span>="@android:id/list"<span style="color: #000000;">
        android:layout_width</span>="match_parent"<span style="color: #000000;">
        android:layout_height</span>="match_parent"
        /&gt;

    &lt;<span style="color: #000000;">TextView
        android:id</span>="@android:id/empty"<span style="color: #000000;">
        android:layout_width</span>="match_parent"<span style="color: #000000;">
        android:layout_height</span>="match_parent"<span style="color: #000000;">
        android:gravity</span>="center"<span style="color: #000000;">
        android:text</span>="@string/activity_list_empty"<span style="color: #000000;">
        android:visibility</span>="gone"<span style="color: #000000;">
        android:textAppearance</span>="?android:attr/textAppearanceMedium"
        /&gt;

&lt;/FrameLayout&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">activity_list_item_2.xml 列表控件的子项，存放应用名称和应用logo。drawablePadding="14dip".</span>
&lt;TextView xmlns:android="http://schemas.android.com/apk/res/android"<span style="color: #000000;">
    android:layout_width</span>="match_parent"<span style="color: #000000;">
    android:layout_height</span>="wrap_content"<span style="color: #000000;">
    android:minHeight</span>="?attr/listPreferredItemHeight"<span style="color: #000000;">
    android:textAppearance</span>="?attr/textAppearanceListItemSmall"<span style="color: #000000;">
    android:gravity</span>="center_vertical"<span style="color: #000000;">
    android:drawablePadding</span>="14dip"<span style="color: #000000;">
    android:paddingStart</span>="?attr/listPreferredItemPaddingStart"<span style="color: #000000;">
    android:paddingEnd</span>="?attr/listPreferredItemPaddingEnd" /&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>后面就进入了Activity的启动流程了。</p>
</div>