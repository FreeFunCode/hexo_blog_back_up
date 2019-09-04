---
title: Android 开源框架 ( 二 ) 基于OkHttp进一步封装的okhttp-utils介绍
date: 2018-07-29 11:24:14
tags: 开源框架
categories: 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/okhttp-utils.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　okhttp-utils是张鸿洋是基于OkHttp封装的框架库。实际工作中，使用的不多，对于小型项目的网络请求和文件传输可以考虑直接使用。否则还是基于主流的OkHttp+Retrift+RxJava框架。</p>
<p>　　对于OkHttp使用，可以自己根据自己项目需要，做一些封装。如果应付手头临时项目，可以借鉴GitHub上一些开源OkHttp封装库.只要去GitHub上搜索下OkHttp 会出来很多封装的框架。</p>
<p>　　分析okhttp-utils使用，只是用于自己学习了解。</p>
<h3>一.将okhttp-utils当做module引入项目中</h3>
<p>　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180729112047366-1984867735.png" alt=""></p>
<h3>二.okhttp-utils引入到项目中文件列表目录：</h3>
<p>　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180729112102229-1521363822.png" alt=""></p>
<h2>三.okhttp-utils 基本使用</h2>
<h3>　　1. //get 请求数据</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils
        .</span><span style="color: #0000ff;">get</span><span style="color: #000000;">()
        .url(url)
        .id(</span><span style="color: #800080;">100</span><span style="color: #000000;">)
        .build()
        .execute(</span><span style="color: #0000ff;">new</span> MyStringCallback());</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　2.//post 请求数据</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils
        .postString()
        .url(url)
        .mediaType(MediaType.parse(</span><span style="color: #800000;">"</span><span style="color: #800000;">application/json; charset=utf-8</span><span style="color: #800000;">"</span><span style="color: #000000;">))
        .content(</span><span style="color: #0000ff;">new</span> Gson().toJson(<span style="color: #0000ff;">new</span> User(<span style="color: #800000;">"</span><span style="color: #800000;">zhy</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">123</span><span style="color: #800000;">"</span><span style="color: #000000;">)))
        .build()
        .execute(</span><span style="color: #0000ff;">new</span> MyStringCallback());        </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　3.//上传文件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils
        .postFile()
        .url(url)
        .file(file)
        .build()
        .execute(</span><span style="color: #0000ff;">new</span> MyStringCallback());</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　4.//加载图片</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils
        .</span><span style="color: #0000ff;">get</span><span style="color: #000000;">()
        .url(url)
        .tag(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">)
        .build()
        .connTimeOut(</span><span style="color: #800080;">20000</span><span style="color: #000000;">)
        .readTimeOut(</span><span style="color: #800080;">20000</span><span style="color: #000000;">)
        .writeTimeOut(</span><span style="color: #800080;">20000</span><span style="color: #000000;">)
        .execute(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> BitmapCallback()
        {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onError(Call call, Exception e, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
                mTv.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">onError:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> e.getMessage());
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onResponse(Bitmap bitmap, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
                Log.e(</span><span style="color: #800000;">"</span><span style="color: #800000;">TAG</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">onResponse：complete</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                mImageView.setImageBitmap(bitmap);
            }
        });    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　5.//上传单个文件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>OkHttpUtils.post()<span style="color: #008000;">//
</span>        .addFile(<span style="color: #800000;">"</span><span style="color: #800000;">mFile</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">messenger_01.png</span><span style="color: #800000;">"</span><span style="color: #000000;">, file)
        .url(url)
        .</span><span style="color: #0000ff;">params</span>(<span style="color: #0000ff;">params</span>)<span style="color: #008000;">//</span><span style="color: #008000;">带表单数据</span>
<span style="color: #000000;">        .headers(headers)
        .build()
        .execute(</span><span style="color: #0000ff;">new</span> MyStringCallback());    </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　6.//上传多个文件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils.post()
        .addFile(</span><span style="color: #800000;">"</span><span style="color: #800000;">mFile</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">messenger_01.png</span><span style="color: #800000;">"</span><span style="color: #000000;">, file)
        .addFile(</span><span style="color: #800000;">"</span><span style="color: #800000;">mFile</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">test1.txt</span><span style="color: #800000;">"</span><span style="color: #000000;">, file2)
        .url(url)
        .</span><span style="color: #0000ff;">params</span>(<span style="color: #0000ff;">params</span>)<span style="color: #008000;">//</span><span style="color: #008000;">带表单数据</span>
<span style="color: #000000;">        .build()
        .execute(</span><span style="color: #0000ff;">new</span> MyStringCallback());    </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　7.//下载文件</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">OkHttpUtils
        .</span><span style="color: #0000ff;">get</span><span style="color: #000000;">()
        .url(url)
        .build()
        .execute(</span><span style="color: #0000ff;">new</span> FileCallBack(Environment.getExternalStorageDirectory().getAbsolutePath(), <span style="color: #800000;">"</span><span style="color: #800000;">gson-2.2.1.jar</span><span style="color: #800000;">"</span><span style="color: #000000;">)
        {

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onBefore(Request request, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> inProgress(<span style="color: #0000ff;">float</span> progress, <span style="color: #0000ff;">long</span> total, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">进度条显示</span>
                mProgressBar.setProgress((<span style="color: #0000ff;">int</span>) (<span style="color: #800080;">100</span> *<span style="color: #000000;"> progress));
                Log.e(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">inProgress :</span><span style="color: #800000;">"</span> + (<span style="color: #0000ff;">int</span>) (<span style="color: #800080;">100</span> *<span style="color: #000000;"> progress));
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onError(Call call, Exception e, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
                Log.e(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">onError :</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> e.getMessage());
            }

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onResponse(File file, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
            {
                Log.e(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">onResponse :</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> file.getAbsolutePath());
            }
        });    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　定义的公共回调方法：</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> MyStringCallback extends StringCallback
    {
        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onBefore(Request request, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
        {
        }

        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onAfter(<span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
        {
        }

        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onError(Call call, Exception e, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
        {
            e.printStackTrace();
        }

        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> onResponse(String response, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
        {
            Log.e(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">onResponse：complete</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            mTv.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">onResponse:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> response);

            </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (id)
            {
                </span><span style="color: #0000ff;">case</span> <span style="color: #800080;">100</span><span style="color: #000000;">:
                    Toast.makeText(MainActivity.</span><span style="color: #0000ff;">this</span>, <span style="color: #800000;">"</span><span style="color: #800000;">http</span><span style="color: #800000;">"</span><span style="color: #000000;">, Toast.LENGTH_SHORT).show();
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">case</span> <span style="color: #800080;">101</span><span style="color: #000000;">:
                    Toast.makeText(MainActivity.</span><span style="color: #0000ff;">this</span>, <span style="color: #800000;">"</span><span style="color: #800000;">https</span><span style="color: #800000;">"</span><span style="color: #000000;">, Toast.LENGTH_SHORT).show();
                    </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            }
        }

        @Override
        </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> inProgress(<span style="color: #0000ff;">float</span> progress, <span style="color: #0000ff;">long</span> total, <span style="color: #0000ff;">int</span><span style="color: #000000;"> id)
        {
            Log.e(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">inProgress:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> progress);
            mProgressBar.setProgress((</span><span style="color: #0000ff;">int</span>) (<span style="color: #800080;">100</span> *<span style="color: #000000;"> progress));
        }
    }        </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>