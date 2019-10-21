---
title: Android 开源框架 ( 四 ) Afinal --- Android 里的 ORM IOC聚合型框架
date: 2018-07-30 23:07:12
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/afinal.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>Afinal&nbsp;是一个android的sqlite的 orm 和 ioc 框架.是一种聚合型框架, 大而全。所以不推荐使用,只做了解即可.应付手头临时项目.</p>
<p>推荐阅读,<a href="https://www.jianshu.com/p/f3227c7008d4" target="_blank">这么多开源框架，该用哪个好</a>？:</p>
<h2>一.引言</h2>
<p>　　Afinal是一个开源的android的orm和ioc应用开发框架.在android应用开发中，FinalActivity模块通过Afinal的ioc框架，诸如ui绑定，事件绑定，通过注解可以自动绑定。Afinal的orm框架，很轻松的就可以对android的sqlite数据库进行增删改查操作,Afinal内嵌了finalHttp等简单易用的工具，可以轻松的对http进行请求操作</p>
<h2>二.注解介绍</h2>
<p>　　说道注解首先接触最多的是@Override: 表示该方法是重写父类中的方法，编译的时候会检查该方法，如果这个方法不是父类中存在的将会报错.</p>
<p>　　java.lang.annotation 中包含所有定义自定义注解所需用到的原注解和接口。接口java.lang.annotation.Annotation 是所有注解继承的接口,并且是自动继承，不需要定义时指定，类似于所有类都自动继承Object。所以很多java.lang.annotation种的注册我们可以直接使用.</p>
<h3>　　注解三种保留方式: 　　</h3>
<p>　　@Retention: 定义注解的保留策略</p>
<p> 　　@Retention(RetentionPolicy.SOURCE)//注解仅存在于源码中，在class字节码文件中不包含<br>    　　@Retention(RetentionPolicy.CLASS)// 默认的保留策略，注解会在class字节码文件中存在，但运行时无法得<br>    　　@Retention(RetentionPolicy.RUNTIME)// 注解会在class字节码文件中存在，在运行时可以通过反射获取到</p>
<p>	　　1.SOURCE:只保留在源码中，不保留在class中，同时也不加载到虚拟机中. <br>	　　2.CLASS:保留在源码中，同时也保留到class中，但是不加载到虚拟机中.在程序编译时根据注解进行一些额外的操作，大名鼎鼎的<strong>ButterKnife</strong>运用的就是编译时注解,ButterKnife在我们编译时，就根据注解，自动生成了一些辅助类。 <br>	　　3.RUNING:保留到源码中，同时也保留到class中，最后加载到虚拟机中.在运行时环境下运用反射，动态获取对象、属性、方法等，一般的IOC框架就是这样，可能会牺牲一点效率。 <strong>EventBus</strong>是使用运行时注解，主要的作用是在运行的时候会去查找所有被注解的方法，然后再去解析注解。运行时注解会影响程序的性能，毕竟在运行的时候有一个查找的过程，所以运行时注解的作用一般是标记一个作用区。</p>
<h2>三.Afinal 介绍<em id="__mceDel"><br></em></h2>
<h3><em id="__mceDel">　　Afinal的四大模块：</em></h3>
<p>    　　　　FinalDB模块：android中的orm框架，一行代码就可以进行增删改查。支持一对多，多对一等查询。</p>
<p>    　　　　FinalActivity模块：android中的ioc框架，完全注解方式就可以进行UI绑定和事件绑定。无需findViewById和setClickListener等。</p>
<p>    　　　　FinalHttp模块：通过httpclient进行封装http数据请求，支持ajax方式加载。</p>
<p>    　　　　FinalBitmap模块：通过FinalBitmap，imageview加载bitmap的时候无需考虑bitmap加载过程中出现的oom和android容器快速滑动时候出现的图片错位等现象。FinalBitmap可以配置线程加载线程数量，缓存大小，缓存路径，加载显示动画等。FinalBitmap的内存管理使用lru算法，没有使用弱引用（android2.3以后google已经不建议使用弱引用，android2.3后强行回收软引用和弱引用，详情查看android官方文档），更好的管理bitmap内存。FinalBitmap可以自定义下载器，用来扩展其他协议显示网络图片，比如ftp等。同时可以自定义bitmap显示器，在imageview显示图片的时候播放动画等（默认是渐变动画显示）。</p>
<h2>&nbsp;四.Afinal 使用</h2>
<h4>&nbsp;　&nbsp; 1.引入jar包</h4>
<h4>	　　2.授权</h4>
<div class="cnblogs_code">
<pre>&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.INTERNET</span><span style="color: #800000;">"</span> /&gt;
&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.WRITE_EXTERNAL_STORAGE</span><span style="color: #800000;">"</span> /&gt;</pre>
</div>
<h4>　　3.FinalDB使用</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>FinalDb db = FinalDb.create(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
User user </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> User();
user.setEmail(</span><span style="color: #800000;">"1@</span><span style="color: #800000;">qq.com</span><span style="color: #800000;">"</span><span style="color: #000000;">);
user.setId(</span><span style="color: #800080;">1</span><span style="color: #000000;">);
user.setName(</span><span style="color: #800000;">"</span><span style="color: #800000;">oreo</span><span style="color: #800000;">"</span><span style="color: #000000;">); 
db.save(user);</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　查看下源码：（最后还是转成sql执行）</p>
<div class="cnblogs_code">
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> save(Object entity) {
        </span><span style="color: #0000ff;">this</span><span style="color: #000000;">.checkTableExist(entity.getClass());
        </span><span style="color: #0000ff;">this</span><span style="color: #000000;">.exeSqlInfo(SqlBuilder.buildInsertSql(entity));
    }</span></pre>
</div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span><span style="color: #000000;"> SqlInfo buildInsertSql(Object entity) {
        List</span>&lt;KeyValue&gt; keyValueList =<span style="color: #000000;"> getSaveKeyValueListByEntity(entity);
        StringBuffer strSQL </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> StringBuffer();
        SqlInfo sqlInfo </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
        </span><span style="color: #0000ff;">if</span>(keyValueList != <span style="color: #0000ff;">null</span> &amp;&amp; keyValueList.size() &gt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
            sqlInfo </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> SqlInfo();
            strSQL.append(</span><span style="color: #800000;">"</span><span style="color: #800000;">INSERT INTO </span><span style="color: #800000;">"</span><span style="color: #000000;">);
            strSQL.append(TableInfo.</span><span style="color: #0000ff;">get</span><span style="color: #000000;">(entity.getClass()).getTableName());
            strSQL.append(</span><span style="color: #800000;">"</span><span style="color: #800000;"> (</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            Iterator var5 </span>=<span style="color: #000000;"> keyValueList.iterator();

            </span><span style="color: #0000ff;">while</span><span style="color: #000000;">(var5.hasNext()) {
                KeyValue kv </span>=<span style="color: #000000;"> (KeyValue)var5.next();
                strSQL.append(kv.getKey()).append(</span><span style="color: #800000;">"</span><span style="color: #800000;">,</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                sqlInfo.addValue(kv.getValue());
            }

            strSQL.deleteCharAt(strSQL.length() </span>- <span style="color: #800080;">1</span><span style="color: #000000;">);
            strSQL.append(</span><span style="color: #800000;">"</span><span style="color: #800000;">) VALUES ( </span><span style="color: #800000;">"</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">int</span> length =<span style="color: #000000;"> keyValueList.size();

            </span><span style="color: #0000ff;">for</span>(<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; length; ++<span style="color: #000000;">i) {
                strSQL.append(</span><span style="color: #800000;">"</span><span style="color: #800000;">?,</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            }

            strSQL.deleteCharAt(strSQL.length() </span>- <span style="color: #800080;">1</span><span style="color: #000000;">);
            strSQL.append(</span><span style="color: #800000;">"</span><span style="color: #800000;">)</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            sqlInfo.setSql(strSQL.toString());
        }

        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> sqlInfo;
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>　　4.FinalBitmap使用</h2>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> 开始加载图片</span>
finalBitmap.display(iv_afinal,<span style="color: #800000;">"</span><span style="color: #800000;">https://images2018.cnblogs.com/blog/612293/201807/612293-20180722160006222-1427704878.jpg</span><span style="color: #800000;">"</span>);</pre>
</div>
<p>　　查看下源码:</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> doDisplay(View imageView, String uri, BitmapDisplayConfig displayConfig) {
        </span><span style="color: #0000ff;">if</span>(!<span style="color: #0000ff;">this</span><span style="color: #000000;">.mInit) {
            </span><span style="color: #0000ff;">this</span><span style="color: #000000;">.init();
        }

        </span><span style="color: #0000ff;">if</span>(!TextUtils.isEmpty(uri) &amp;&amp; imageView != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">if</span>(displayConfig == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                displayConfig </span>= <span style="color: #0000ff;">this</span><span style="color: #000000;">.mConfig.defaultDisplayConfig;
            }

            Bitmap bitmap </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">if</span>(<span style="color: #0000ff;">this</span>.mImageCache != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {<br>　　　　　　　　　　//优先缓存获取<br>                bitmap </span>= <span style="color: #0000ff;">this</span><span style="color: #000000;">.mImageCache.getBitmapFromMemoryCache(uri);
            }

            </span><span style="color: #0000ff;">if</span>(bitmap != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;">(imageView instanceof ImageView) {
                    ((ImageView)imageView).setImageBitmap(bitmap);
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    imageView.setBackgroundDrawable(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> BitmapDrawable(bitmap));
                }
            } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span><span style="color: #000000;">(checkImageTask(uri, imageView)) {<br>　　　　　　　　　//FinalBitmap模块 加载图片
                FinalBitmap.BitmapLoadAndDisplayTask task </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> FinalBitmap.BitmapLoadAndDisplayTask(imageView, displayConfig);
                FinalBitmap.AsyncDrawable asyncDrawable </span>= <span style="color: #0000ff;">new</span> FinalBitmap.AsyncDrawable(<span style="color: #0000ff;">this</span><span style="color: #000000;">.mContext.getResources(), displayConfig.getLoadingBitmap(), task);
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;">(imageView instanceof ImageView) {
                    ((ImageView)imageView).setImageDrawable(asyncDrawable);
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    imageView.setBackgroundDrawable(asyncDrawable);
                }

                task.executeOnExecutor(</span><span style="color: #0000ff;">this</span>.bitmapLoadAndDisplayExecutor, <span style="color: #0000ff;">new</span><span style="color: #000000;"> Object[]{uri});
            }

        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>　　5.FinalHttp下载文件</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>FinalHttp finalHttp = <span style="color: #0000ff;">new</span><span style="color: #000000;"> FinalHttp();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 请求网络资源的地址</span>
        String url = <span style="color: #800000;">""</span><span style="color: #000000;">;
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 存放视频文件到本地位置</span>
        String target = getFilesDir()+<span style="color: #800000;">"</span><span style="color: #800000;">/testAfinal.mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">;

        finalHttp.download(url, target, </span><span style="color: #0000ff;">new</span> AjaxCallBack&lt;File&gt;<span style="color: #000000;">() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onSuccess(File file) {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">下载文件成功</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onSuccess(file);
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onFailure(Throwable t, <span style="color: #0000ff;">int</span><span style="color: #000000;"> errorNo, String strMsg) {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">下载文件失败</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onFailure(t, errorNo, strMsg);
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onStart() {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">开始下载</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onStart();
            }
        });</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>　　6.FinalHttp上传文件</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>FinalHttp finalHttp = <span style="color: #0000ff;">new</span><span style="color: #000000;"> FinalHttp();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 文件上传到服务器的位置</span>
        String url  = <span style="color: #800000;">""</span><span style="color: #000000;">;
        AjaxParams </span><span style="color: #0000ff;">params</span> = <span style="color: #0000ff;">new</span><span style="color: #000000;"> AjaxParams();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 获取要上传的本地资源</span>
        <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #0000ff;">params</span>.put(<span style="color: #800000;">"</span><span style="color: #800000;">File</span><span style="color: #800000;">"</span>,<span style="color: #0000ff;">new</span> File(getFilesDir()+<span style="color: #800000;">"</span><span style="color: #800000;">/testAfinal.mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">));
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (FileNotFoundException e) {
            e.printStackTrace();
        }

        finalHttp.post(url, </span><span style="color: #0000ff;">params</span>, <span style="color: #0000ff;">new</span> AjaxCallBack&lt;Object&gt;<span style="color: #000000;">() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onStart() {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">开始上传</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onStart();
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onSuccess(Object o) {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">上传成功</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onSuccess(o);
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onFailure(Throwable t, <span style="color: #0000ff;">int</span><span style="color: #000000;"> errorNo, String strMsg) {
                tv_afinal_result.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">上传失败</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                super.onFailure(t, errorNo, strMsg);
            }
        });</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>