---
title: Android 开源框架 ( 三 ) 基于OkHttp进一步封装的OkHttpUtils介绍
date: 2018-07-29 22:51:43
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/OkHttpUtils.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　OkHttpUtils是 廖子尧 是基于OkHttp封装的框架库。里面也封装了很多其他实用的一些组件，这里只介绍下网络相关的使用。</p>
<p>　　里面的<strong>上传下载功能使用队列的概念</strong>做了进一步封装，但是因为我使用的是旧库，对于android6.0运行时权限判断和android7.0私有文件权限设置没有处理。</p>
<p>　　同上一篇随笔一样分析：<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/okhttp-utils.html" target="_blank">Android 开源框架 ( 二 ) 基于OkHttp进一步封装的okhttp-utils介绍</a> 介绍下基本使用，加深自己对OkHttp的理解。</p>
<h3>　　一.引入到自己项目后，先来对比下同上一篇介绍的okhttp-utils类库目录对比下：</h3>
<p>　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180729112723166-1670399309.png" alt=""></p>
<p>　　注意：途中下面红框标注部分的module:okhttputils-lib是本文介绍的OkHttpUtils类库，上面的module:okhttputils是<a id="cb_post_title_url" class="postTitle2" href="https://www.cnblogs.com/bugzone/p/okhttp-utils.html" target="_blank">Android 开源框架 ( 二 ) 基于OkHttp进一步封装的okhttp-utils介绍</a>介绍的框架类库。　　</p>
<p>　　两个封装框架都有一个 <strong>OkHttpUtils 入口类。</strong></p>
<p>&nbsp;</p>
<h2><strong>　　二. OkHttpUtils基本使用</strong></h2>
<h3><strong>　　1</strong>.//get 请求数据</h3>
<div class="cnblogs_code">
<pre>OkHttpUtils.<span style="color: #0000ff;">get</span>(Urls.URL_METHOD)<span style="color: #008000;">//
</span>            .tag(<span style="color: #0000ff;">this</span>)<span style="color: #008000;">//
</span>            .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>            .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>            .execute(<span style="color: #0000ff;">new</span> MethodCallBack&lt;&gt;(<span style="color: #0000ff;">this</span>, RequestInfo.<span style="color: #0000ff;">class</span>));</pre>
</div>
<h3>　　2.//post 请求数据</h3>
<div class="cnblogs_code">
<pre>OkHttpUtils.post(Urls.URL_METHOD)<span style="color: #008000;">//
</span>            .tag(<span style="color: #0000ff;">this</span>)<span style="color: #008000;">//
</span>            .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>            .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>            .execute(<span style="color: #0000ff;">new</span> MethodCallBack&lt;&gt;(<span style="color: #0000ff;">this</span>, RequestInfo.<span style="color: #0000ff;">class</span>));    </pre>
</div>
<h3>　　3.//缓存请求</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>OkHttpUtils.<span style="color: #0000ff;">get</span>(Urls.URL_CACHE)<span style="color: #008000;">//
</span>        .tag(<span style="color: #0000ff;">this</span>)<span style="color: #008000;">//
</span>        .cacheMode(CacheMode.DEFAULT)<span style="color: #008000;">//
</span>        .cacheKey(<span style="color: #800000;">"</span><span style="color: #800000;">cache_default</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>        .cacheTime(<span style="color: #800080;">5000</span>)<span style="color: #008000;">//</span><span style="color: #008000;">对于默认的缓存模式,该时间无效,依靠的是服务端对304缓存的控制</span>
        .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>        .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>        .execute(<span style="color: #0000ff;">new</span> CacheCallBack(<span style="color: #0000ff;">this</span>));    </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">enum</span><span style="color: #000000;"> CacheMode {
            </span><span style="color: #008000;">/*</span><span style="color: #008000;">* 按照HTTP协议的默认缓存规则，例如有304响应头时缓存 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            DEFAULT,

            </span><span style="color: #008000;">/*</span><span style="color: #008000;">* 不使用缓存 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            NO_CACHE,

            </span><span style="color: #008000;">/*</span><span style="color: #008000;">* 请求网络失败后，读取缓存 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            REQUEST_FAILED_READ_CACHE,

            </span><span style="color: #008000;">/*</span><span style="color: #008000;">* 如果缓存不存在才请求网络，否则使用缓存 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            IF_NONE_CACHE_REQUEST,

            </span><span style="color: #008000;">/*</span><span style="color: #008000;">* 先使用缓存，不管是否存在，仍然请求网络 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
            FIRST_CACHE_THEN_REQUEST,
        }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　4.//批量上传文件</h3>
<h4>　　　　4.1 方法一：　　</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>OkHttpUtils.post(Urls.URL_FORM_UPLOAD)<span style="color: #008000;">//
</span>                .tag(<span style="color: #0000ff;">this</span>)<span style="color: #008000;">//
</span>                .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>                .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header2</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue2</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>                .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>                .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param2</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue2</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//</span>
<span style="color: #008000;">//</span><span style="color: #008000;">                .params("file1",new File("文件路径"))   </span><span style="color: #008000;">//</span><span style="color: #008000;">这种方式为一个key，对应一个文件
</span><span style="color: #008000;">//</span><span style="color: #008000;">                .params("file2",new File("文件路径"))
</span><span style="color: #008000;">//</span><span style="color: #008000;">                .params("file3",new File("文件路径"))</span>
                .addFileParams(<span style="color: #800000;">"</span><span style="color: #800000;">file</span><span style="color: #800000;">"</span>, files)           <span style="color: #008000;">//</span><span style="color: #008000;"> 这种方式为同一个key，上传多个文件</span>
                .execute(<span style="color: #0000ff;">new</span> ProgressUpCallBack&lt;&gt;(<span style="color: #0000ff;">this</span>, RequestInfo.<span style="color: #0000ff;">class</span>));</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　　　4.2 方式二：</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; images.size(); i++<span style="color: #000000;">) {
    </span><span style="color: #008000;">//</span><span style="color: #008000;">注册监听</span>
    MyUploadListener listener = <span style="color: #0000ff;">new</span><span style="color: #000000;"> MyUploadListener();
    listener.setUserTag(gridView.getChildAt(i));
    </span><span style="color: #008000;">//</span><span style="color: #008000;">批量加入上传队列</span>
    UploadManager.getInstance(getContext()).addTask(Urls.URL_FORM_UPLOAD, <span style="color: #0000ff;">new</span> File(images.<span style="color: #0000ff;">get</span>(i).path), <span style="color: #800000;">"</span><span style="color: #800000;">imageFile</span><span style="color: #800000;">"</span><span style="color: #000000;">, listener);
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/*</span><span style="color: #008000;">* 一旦该方法执行，意味着开始下载了 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">protected</span> UploadInfo doInBackground(Void... <span style="color: #0000ff;">params</span><span style="color: #000000;">) {
        </span><span style="color: #0000ff;">if</span> (isCancelled()) <span style="color: #0000ff;">return</span><span style="color: #000000;"> mUploadInfo;
        L.e(</span><span style="color: #800000;">"</span><span style="color: #800000;">doInBackground:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> mUploadInfo.getResourcePath());
        mUploadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
        mUploadInfo.setState(UploadManager.UPLOADING);
        postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;">构建请求体,默认使用post请求上传</span>
<span style="color: #000000;">        Response response;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">本质还是调用OkHttpUtils的post方法上传</span>
            PostRequest postRequest =<span style="color: #000000;"> OkHttpUtils.post(mUploadInfo.getUrl());
            File resource </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(mUploadInfo.getResourcePath());
            </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (TextUtils.isEmpty(mUploadInfo.getFileName())) {
                mUploadInfo.setFileName(resource.getName());
            }
            postRequest.</span><span style="color: #0000ff;">params</span><span style="color: #000000;">(mUploadInfo.getKey(), resource, mUploadInfo.getFileName());
            </span><span style="color: #008000;">//</span><span style="color: #008000;">接口对接，数据回调</span>
            postRequest.setCallback(<span style="color: #0000ff;">new</span><span style="color: #000000;"> MergeListener());
            response </span>=<span style="color: #000000;"> postRequest.execute();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException e) {
            e.printStackTrace();
            mUploadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mUploadInfo.setState(UploadManager.ERROR);
            postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #800000;">"</span><span style="color: #800000;">网络异常</span><span style="color: #800000;">"</span><span style="color: #000000;">, e);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mUploadInfo;
        }

        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (response.isSuccessful()) {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">解析过程中抛出异常，一般为 json 格式错误，或者数据解析异常</span>
            <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                T t </span>=<span style="color: #000000;"> (T) mUploadInfo.getListener().parseNetworkResponse(response);
                mUploadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
                mUploadInfo.setState(UploadManager.FINISH); </span><span style="color: #008000;">//</span><span style="color: #008000;">上传成功</span>
                postMessage(t, <span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mUploadInfo;
            } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
                e.printStackTrace();
                mUploadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
                mUploadInfo.setState(UploadManager.ERROR);
                postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #800000;">"</span><span style="color: #800000;">解析数据对象出错</span><span style="color: #800000;">"</span><span style="color: #000000;">, e);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mUploadInfo;
            }
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            mUploadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mUploadInfo.setState(UploadManager.ERROR);
            postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #800000;">"</span><span style="color: #800000;">数据返回失败</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mUploadInfo;
        }
    }    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h3>　　5.//批量下载文件</h3>
<h4>　　　　5.1 方式一：</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>OkHttpUtils.<span style="color: #0000ff;">get</span>(Urls.URL_DOWNLOAD)<span style="color: #008000;">//
</span>                .tag(<span style="color: #0000ff;">this</span>)<span style="color: #008000;">//
</span>                .headers(<span style="color: #800000;">"</span><span style="color: #800000;">header1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">headerValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>                .<span style="color: #0000ff;">params</span>(<span style="color: #800000;">"</span><span style="color: #800000;">param1</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">paramValue1</span><span style="color: #800000;">"</span>)<span style="color: #008000;">//
</span>                .execute(<span style="color: #0000ff;">new</span> DownloadFileCallBack(Environment.getExternalStorageDirectory() + <span style="color: #800000;">"</span><span style="color: #800000;">/temp</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">OkHttpUtils.apk</span><span style="color: #800000;">"</span>));</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　　　5.2 方式二（DownloadManager）：</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">　　　　if</span> (downloadManager.getTaskByUrl(apk.getUrl()) != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            Toast.makeText(getContext(), </span><span style="color: #800000;">"</span><span style="color: #800000;">任务已经在下载列表中</span><span style="color: #800000;">"</span><span style="color: #000000;">, Toast.LENGTH_SHORT).show();
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            downloadManager.addTask(apk.getUrl(), </span><span style="color: #0000ff;">null</span><span style="color: #000000;">);
            AppCacheUtils.getInstance(getContext()).put(apk.getUrl(), apk);
            download.setText(</span><span style="color: #800000;">"</span><span style="color: #800000;">已在队列</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            download.setEnabled(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
        }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/*</span><span style="color: #008000;">* 一旦该方法执行，意味着开始下载了 </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @Override
    </span><span style="color: #0000ff;">protected</span> DownloadInfo doInBackground(Void... <span style="color: #0000ff;">params</span><span style="color: #000000;">) {
        </span><span style="color: #0000ff;">if</span> (isCancelled()) <span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        L.e(</span><span style="color: #800000;">"</span><span style="color: #800000;">doInBackground:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> mDownloadInfo.getFileName());
        mPreviousTime </span>=<span style="color: #000000;"> System.currentTimeMillis();
        mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
        mDownloadInfo.setState(DownloadManager.DOWNLOADING);
        postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;">构建下载文件路径，如果有设置，就用设置的，否者就自己创建</span>
        String url =<span style="color: #000000;"> mDownloadInfo.getUrl();
        String fileName </span>=<span style="color: #000000;"> mDownloadInfo.getFileName();
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (TextUtils.isEmpty(fileName)) {
            fileName </span>=<span style="color: #000000;"> getUrlFileName(url);
            mDownloadInfo.setFileName(fileName);
        }
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (TextUtils.isEmpty(mDownloadInfo.getTargetPath())) {
            File file </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(mDownloadInfo.getTargetFolder(), fileName);
            mDownloadInfo.setTargetPath(file.getAbsolutePath());
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;">检查手机上文件的有效性</span>
        File file = <span style="color: #0000ff;">new</span><span style="color: #000000;"> File(mDownloadInfo.getTargetPath());
        </span><span style="color: #0000ff;">long</span><span style="color: #000000;"> startPos;
        </span><span style="color: #0000ff;">if</span> (file.length() !=<span style="color: #000000;"> mDownloadInfo.getDownloadLength()) {
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR);
            postMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">断点文件异常，需要删除后重新下载</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            </span><span style="color: #008000;">//</span><span style="color: #008000;">断点下载的情况</span>
            startPos =<span style="color: #000000;"> mDownloadInfo.getDownloadLength();
        }
        </span><span style="color: #008000;">//</span><span style="color: #008000;">再次检查文件有效性，文件大小大于总文件大小</span>
        <span style="color: #0000ff;">if</span> (startPos &gt;<span style="color: #000000;"> mDownloadInfo.getTotalLength()) {
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR);
            postMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">断点文件异常，需要删除后重新下载</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        }
        </span><span style="color: #0000ff;">if</span> (startPos == mDownloadInfo.getTotalLength() &amp;&amp; startPos &gt; <span style="color: #800080;">0</span><span style="color: #000000;">) {
            mDownloadInfo.setProgress(</span><span style="color: #800080;">1.0f</span><span style="color: #000000;">);
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.FINISH);
            postMessage(</span><span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        }
        </span><span style="color: #008000;">//</span><span style="color: #008000;">设置断点写文件</span>
<span style="color: #000000;">        ProgressRandomAccessFile randomAccessFile;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            randomAccessFile </span>= <span style="color: #0000ff;">new</span> ProgressRandomAccessFile(file, <span style="color: #800000;">"</span><span style="color: #800000;">rw</span><span style="color: #800000;">"</span><span style="color: #000000;">, startPos);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (FileNotFoundException e) {
            e.printStackTrace();
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR);
            postMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">没有找到已存在的断点文件</span><span style="color: #800000;">"</span><span style="color: #000000;">, e);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        }
        L.e(</span><span style="color: #800000;">"</span><span style="color: #800000;">startPos:</span><span style="color: #800000;">"</span> + startPos + <span style="color: #800000;">"</span><span style="color: #800000;">  path:</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> mDownloadInfo.getTargetPath());

        </span><span style="color: #008000;">//</span><span style="color: #008000;">构建请求体,默认使用get请求下载,设置断点头</span>
<span style="color: #000000;">        Response response;
        </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            response </span>= OkHttpUtils.<span style="color: #0000ff;">get</span>(url).headers(<span style="color: #800000;">"</span><span style="color: #800000;">RANGE</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">bytes=</span><span style="color: #800000;">"</span> + startPos + <span style="color: #800000;">"</span><span style="color: #800000;">-</span><span style="color: #800000;">"</span><span style="color: #000000;">).execute();
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException e) {
            e.printStackTrace();
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR);
            postMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">网络异常</span><span style="color: #800000;">"</span><span style="color: #000000;">, e);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        }
        </span><span style="color: #008000;">//</span><span style="color: #008000;">获取流对象，准备进行读写文件</span>
        <span style="color: #0000ff;">long</span> totalLength =<span style="color: #000000;"> response.body().contentLength();
        </span><span style="color: #0000ff;">if</span> (mDownloadInfo.getTotalLength() == <span style="color: #800080;">0</span><span style="color: #000000;">) {
            mDownloadInfo.setTotalLength(totalLength);
        }
        InputStream </span><span style="color: #0000ff;">is</span> =<span style="color: #000000;"> response.body().byteStream();
        </span><span style="color: #008000;">//</span><span style="color: #008000;">读写文件流</span>
        <span style="color: #0000ff;">try</span><span style="color: #000000;"> {
            download(</span><span style="color: #0000ff;">is</span><span style="color: #000000;">, randomAccessFile);
        } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException e) {
            e.printStackTrace();
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR);
            postMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">文件读写异常</span><span style="color: #800000;">"</span><span style="color: #000000;">, e);
            </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
        }

        </span><span style="color: #008000;">//</span><span style="color: #008000;">循环结束走到这里，a.下载完成     b.暂停      c.判断是否下载出错</span>
        <span style="color: #0000ff;">if</span><span style="color: #000000;"> (isCancelled()) {
            L.e(</span><span style="color: #800000;">"</span><span style="color: #800000;">state: 暂停</span><span style="color: #800000;">"</span> +<span style="color: #000000;"> mDownloadInfo.getState());
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            </span><span style="color: #0000ff;">if</span> (isPause) mDownloadInfo.setState(DownloadManager.PAUSE); <span style="color: #008000;">//</span><span style="color: #008000;">暂停</span>
            <span style="color: #0000ff;">else</span> mDownloadInfo.setState(DownloadManager.NONE);          <span style="color: #008000;">//</span><span style="color: #008000;">停止</span>
            postMessage(<span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (file.length() == mDownloadInfo.getTotalLength() &amp;&amp; mDownloadInfo.getState() ==<span style="color: #000000;"> DownloadManager.DOWNLOADING) {
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.FINISH); </span><span style="color: #008000;">//</span><span style="color: #008000;">下载完成</span>
            postMessage(<span style="color: #0000ff;">null</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
        } </span><span style="color: #0000ff;">else</span> <span style="color: #0000ff;">if</span> (file.length() !=<span style="color: #000000;"> mDownloadInfo.getDownloadLength()) {
            mDownloadInfo.setNetworkSpeed(</span><span style="color: #800080;">0</span><span style="color: #000000;">);
            mDownloadInfo.setState(DownloadManager.ERROR); </span><span style="color: #008000;">//</span><span style="color: #008000;">由于不明原因，文件保存有误</span>
            postMessage(<span style="color: #800000;">"</span><span style="color: #800000;">未知原因</span><span style="color: #800000;">"</span>, <span style="color: #0000ff;">null</span><span style="color: #000000;">);
        }
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> mDownloadInfo;
    }    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>