---
title: Android 开源框架 ( 五 ) xUtils --- Android 里的 ORM IOC聚合型框架
date: 2018-07-30 23:55:54
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/Xutils.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　xUtils同Afinal一样属于聚合型框架, 大而全，但是越容易牵一发而动全身。所以不推荐使用,只做了解即可.应付手头临时项目.</p>
<p>　　<a id="post_title_link_9392534" href="https://www.cnblogs.com/bugzone/p/afinal.html" target="_blank">Android 开源框架 ( 四 ) Afinal --- Android 里的 ORM IOC聚合型框架</a></p>
<h2>一.Xutils 介绍</h2>
<p>　　Xutils是基于afinal开发的，但是比afinal稳定性提高了不少.xUtils 最初源于Afinal框架，进行了大量重构，使得xUtils支持大文件上传，更全面的http请求协议支持(10种谓词)，拥有更加灵活的ORM，更多的事件注解支持且不受混淆影响...<br>　　xUitls最低兼容android 2.2.</p>
<h3>　　XUtils四大模块:&nbsp;</h3>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_80610" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">DbManager&nbsp; 数据库访问接口&lt;br&gt;　　　　android中的orm框架，一行代码就可以进行增删改查；</code></div><div class="line number2 index1 alt1"><code class="csharp plain">　　　　支持事务，默认关闭；</code></div><div class="line number3 index2 alt2"><code class="csharp plain">　　　　可通过注解自定义表名，列名，外键，唯一性约束，NOT NULL约束，CHECK约束等（需要混淆的时候请注解表名和列名）；</code></div><div class="line number4 index3 alt1"><code class="csharp plain">　　　　支持绑定外键，保存实体时外键关联实体自动保存或更新；</code></div><div class="line number5 index4 alt2"><code class="csharp plain">　　　　自动加载外键关联实体，支持延时加载；</code></div><div class="line number6 index5 alt1"><code class="csharp plain">　　　　支持链式表达查询，更直观的查询语义，参考下面的介绍或sample中的例子。　</code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_496238" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">ViewInjector view注入接口</code></div><div class="line number2 index1 alt1"><code class="csharp spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="csharp plain">android中的ioc框架，完全注解方式就可以进行UI，资源和事件绑定；</code></div><div class="line number3 index2 alt2"><code class="csharp spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="csharp plain">新的事件绑定方式，使用混淆工具混淆后仍可正常工作；</code></div><div class="line number4 index3 alt1"><code class="csharp spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="csharp plain">目前支持常用的20种事件绑定，参见ViewCommonEventListener类和包com.lidroid.xutils.view.annotation.</code><code class="csharp keyword">event</code><code class="csharp plain">。</code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_858832" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">HttpManager http请求接口</code></div><div class="line number2 index1 alt1"><code class="csharp spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="csharp plain">　　支持同步，异步方式的请求；</code></div><div class="line number3 index2 alt2"><code class="csharp plain">　　　　支持大文件上传，上传大文件不会oom；</code></div><div class="line number4 index3 alt1"><code class="csharp plain">　　　　支持GET，POST，PUT，MOVE，COPY，DELETE，HEAD，OPTIONS，TRACE，CONNECT请求；</code></div><div class="line number5 index4 alt2"><code class="csharp plain">　　　　下载支持301/302重定向，支持设置是否根据Content-Disposition重命名下载的文件；</code></div><div class="line number6 index5 alt1"><code class="csharp plain">　　　　返回文本内容的请求(默认只启用了GET请求)支持缓存，可设置默认过期时间和针对当前请求的过期时间。&nbsp;&nbsp;&nbsp; </code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_93637" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">ImageManager 图片绑定接口</code></div><div class="line number2 index1 alt1"><code class="csharp spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="csharp plain">　　加载bitmap的时候无需考虑bitmap加载过程中出现的oom和android容器快速滑动时候出现的图片错位等现象；</code></div><div class="line number3 index2 alt2"><code class="csharp plain">　　　　支持加载网络图片和本地图片；</code></div><div class="line number4 index3 alt1"><code class="csharp plain">　　　　内存管理使用lru算法，更好的管理bitmap内存；</code></div><div class="line number5 index4 alt2"><code class="csharp plain">　　　　可配置线程加载线程数量，缓存大小，缓存路径，加载显示动画等...</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3>　　混淆配置:</h3>
<p> 　　　　1. 添加Android默认混淆配置${sdk.dir}/tools/proguard/proguard-android.txt<br>	　　　　2. 不要混淆xUtils中的注解类型，添加混淆配置：-keep class * extends java.lang.annotation.Annotation { *; }<br>	　　　　3. 对使用DbUtils模块持久化的实体类不要混淆，或者注解所有表和列名称@Table(name="xxx")，@Id(column="xxx")，@Column(column="xxx"),@Foreign(column="xxx",foreign="xxx")；</p>
<h2>二.Xutils 使用</h2>
<h4>1.引入示例module</h4>
<p>　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180730233934163-175096619.png" alt=""></p>
<h4>2.记得授权</h4>
<div class="cnblogs_code">
<pre>&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.INTERNET</span><span style="color: #800000;">"</span> /&gt;
&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.WRITE_EXTERNAL_STORAGE</span><span style="color: #800000;">"</span> /&gt;</pre>
</div>
<h4>3.Application里初始化</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> MyApplication extends Application {

    @Override
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onCreate() {
        super.onCreate();
        x.Ext.init(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
        x.Ext.setDebug(BuildConfig.DEBUG); </span><span style="color: #008000;">//</span><span style="color: #008000;"> 开启debug会影响性能</span>
<span style="color: #000000;">    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>4.DbManager操作数据库</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>DbManager.DaoConfig daoConfig = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DbManager.DaoConfig()
            .setDbName(</span><span style="color: #800000;">"</span><span style="color: #800000;">test.db</span><span style="color: #800000;">"</span><span style="color: #000000;">)
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 不设置dbDir时, 默认存储在app的私有目录.</span>
            .setDbDir(<span style="color: #0000ff;">new</span> File(<span style="color: #800000;">"</span><span style="color: #800000;">/sdcard</span><span style="color: #800000;">"</span>)) <span style="color: #008000;">//</span><span style="color: #008000;"> "sdcard"的写法并非最佳实践, 这里为了简单, 先这样写了.</span>
            .setDbVersion(<span style="color: #800080;">2</span><span style="color: #000000;">)
            .setDbOpenListener(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> DbManager.DbOpenListener() {
                @Override
                </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onDbOpened(DbManager db) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 开启WAL, 对写入加速提升巨大</span>
<span style="color: #000000;">                    db.getDatabase().enableWriteAheadLogging();
                }
            })
            .setDbUpgradeListener(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> DbManager.DbUpgradeListener() {
                @Override
                </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onUpgrade(DbManager db, <span style="color: #0000ff;">int</span> oldVersion, <span style="color: #0000ff;">int</span><span style="color: #000000;"> newVersion) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> TODO: ...
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> db.addColumn(...);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> db.dropTable(...);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> ...
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> or
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> db.dropDb();</span>
<span style="color: #000000;">                }
            });</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre> DbManager db = x.getDb(daoConfig);        </pre>
</div>
<p>最终都是调用xutils lib里的<span style="font-family: Courier New; font-size: small;">db包里的</span>DbModelSelector类转换为sql语句操作sqlLite数据库</p>
<p>　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180730233915251-1868064577.png" alt=""></p>
<h4>5.HttpManager请求，ImageManager加载图片</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> loadImgList(String url) {
        </span><span style="color: #008000;">//</span><span style="color: #008000;">HttpManager异步GET请求</span>
        x.http().<span style="color: #0000ff;">get</span>(<span style="color: #0000ff;">new</span> RequestParams(url), <span style="color: #0000ff;">new</span> Callback.CommonCallback&lt;String&gt;<span style="color: #000000;">() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onSuccess(String result) {
                imageListAdapter.addSrc(getImgSrcList(result));
                imageListAdapter.notifyDataSetChanged();</span><span style="color: #008000;">//</span><span style="color: #008000;">通知listview更新数据</span>
<span style="color: #000000;">            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onError(Throwable ex, boolean isOnCallback) {

            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onCancelled(CancelledException cex) {

            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onFinished() {

            }
        });
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">ImageOptions imageOptions;
    imageOptions </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ImageOptions.Builder()
                .setSize(DensityUtil.dip2px(</span><span style="color: #800080;">120</span>), DensityUtil.dip2px(<span style="color: #800080;">120</span><span style="color: #000000;">))
                .setRadius(DensityUtil.dip2px(</span><span style="color: #800080;">5</span><span style="color: #000000;">))
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> 如果ImageView的大小不是定义为wrap_content, 不要crop.</span>
                .setCrop(<span style="color: #0000ff;">true</span>) <span style="color: #008000;">//</span><span style="color: #008000;"> 很多时候设置了合适的scaleType也不需要它.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> 加载中或错误图片的ScaleType
                </span><span style="color: #008000;">//</span><span style="color: #008000;">.setPlaceholderScaleType(ImageView.ScaleType.MATRIX)</span>
<span style="color: #000000;">                .setImageScaleType(ImageView.ScaleType.CENTER_CROP)
                .setLoadingDrawableId(R.mipmap.ic_launcher)
                .setFailureDrawableId(R.mipmap.ic_launcher)
                .build();</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre>x.image().bind(iv_big_img, getIntent().getStringExtra(<span style="color: #800000;">"</span><span style="color: #800000;">url</span><span style="color: #800000;">"</span>), imageOptions);</pre>
</div>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p> <br><br>	</p>
</div>

