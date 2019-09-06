---
title: Android7.0下载APK后自动安装
date: 2018-07-28 18:57:46
tags: Android7.0
categories: 
- Android
- 知识点
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/strictMode.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>&nbsp; &nbsp; &nbsp; &nbsp; 随着Android版本越来越高，Android对隐私的保护力度也越来越大。这些隐私权限的更改在为用户带来更加安全的操作系统的同时也为开发者带来了一些新的任务。如何让你的APP能够适应这些改变而不是崩溃，是每一位Android开发者必须要了解学习的。<br>		</p>
<h2>一.引言</h2>
<p>	　　Android 6.0引入了动态权限控制.<br>	　　Android 7.0 引入了私有目录被限制访问和StrictMode API ,在7.0上应用私有目录将被限制访问，这与iOS的沙盒机制类似,StrictMode API是指禁止向你的应用外公开 file:// URI。 如果一项包含文件 file:// URI类型 的 Intent 离开你的应用，则会报出异常。这项权限的变更将意味着你无法通过File API访问手机存储上的数据了，基于File API的一些文件浏览器等也将受到很大的影响.<br>	　　所以在7.0系统上,给其他应用传递 file:// URI 类型的Uri，可能会导致接受者无法访问该路径。 因此，在Android7.0中尝试传递 file:// URI 会触发 FileUriExposedException。解决办法就是可以通过使用<strong>FileProvider</strong>来解决这一问题.<br>		</p>
<h2>二. 两种实现自动下载安装方式</h2>
<p>　　两种下载方式入口:</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　/*</span><span style="color: #008000;">*
     * @param context
     * @param apkUrl      APK下载路径
     * @param fileName    APK下载自定义名称
     * @param webViewMode 是否是浏览器模式下载
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> install(Context context, String apkUrl, String fileName, boolean webViewMode) {
        </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (webViewMode) {
            downloadByWeb(context, apkUrl);
        } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
            downloadBySelf(context, apkUrl, fileName);
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　1. 通过手机内置浏览器来下载安装更新APP. 　　　　</h4>
<p>　　这种方式将下载和安装更新操作交给浏览器操作了,很简单,但是这种也是不受控的.有时候浏览器会推荐一系列的广告App伴随下载,不小心就会中招.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;">通过浏览器方式下载并安装</span>
    <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> downloadByWeb(Context context, String apkPath) {
        Uri uri </span>=<span style="color: #000000;"> Uri.parse(apkPath);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">String android.intent.action.VIEW 比较通用，会根据用户的数据类型打开相应的Activity。如:浏览器,电话,播放器,地图</span>
        Intent intent = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Intent(Intent.ACTION_VIEW, uri);
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(intent);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　2. 通过DownloadManager 下载APK,并提示安装</h4>
<h5>　　　　2.1 通过DownloadManager</h5>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">　　　　private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> downloadBySelf(Context context, String apkUrl, String fileName) {
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (TextUtils.isEmpty(apkUrl)) {
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
                }
                </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                    Uri uri </span>=<span style="color: #000000;"> Uri.parse(apkUrl);
                    DownloadManager downloadManager </span>=<span style="color: #000000;"> (DownloadManager) context
                            .getSystemService(Context.DOWNLOAD_SERVICE);
                    DownloadManager.Request request </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> DownloadManager.Request(uri);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">在通知栏中显示</span>
                    request.setVisibleInDownloadsUi(<span style="color: #0000ff;">true</span><span style="color: #000000;">);
                    request.setTitle(</span><span style="color: #800000;">"</span><span style="color: #800000;">应用更新</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                    request.setDescription(</span><span style="color: #800000;">"</span><span style="color: #800000;">本次更新描述</span><span style="color: #800000;">"</span><span style="color: #000000;">)
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">MIME_MapTable是所有文件的后缀名所对应的MIME类型的一个String数组  {".apk",    "application/vnd.android.package-archive"},</span>
                    request.setMimeType(<span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.android.package-archive</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 在通知栏通知下载中和下载完成
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 下载完成后该Notification才会被显示</span>
                    <span style="color: #0000ff;">if</span> (Build.VERSION.SDK_INT &gt;<span style="color: #000000;"> Build.VERSION_CODES.HONEYCOMB) {
                        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Android 3.0版本 以后才有该方法
                        </span><span style="color: #008000;">//</span><span style="color: #008000;">在下载过程中通知栏会一直显示该下载的Notification，在下载完成后该Notification会继续显示，直到用户点击该Notification或者消除该Notification</span>
<span style="color: #000000;">                        request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
                    }
                    String filePath </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                    </span><span style="color: #0000ff;">if</span> (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {<span style="color: #008000;">//</span><span style="color: #008000;">外部存储卡</span>
                        filePath =<span style="color: #000000;"> Environment.getExternalStorageDirectory().getAbsolutePath();
                    } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                        Log.i(TAG, </span><span style="color: #800000;">"</span><span style="color: #800000;">没有SD卡</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                        </span><span style="color: #0000ff;">return</span><span style="color: #000000;">;
                    }
                    downloadUpdateApkFilePath </span>= filePath + File.separator + fileName + System.currentTimeMillis() + <span style="color: #800000;">"</span><span style="color: #800000;">.apk</span><span style="color: #800000;">"</span><span style="color: #000000;">;
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> 若存在，则删除 (这里具体逻辑具体看,我这里是删除)</span>
<span style="color: #000000;">                    deleteFile(downloadUpdateApkFilePath);
                    Uri fileUri </span>= Uri.fromFile(<span style="color: #0000ff;">new</span><span style="color: #000000;"> File(downloadUpdateApkFilePath));
                    request.setDestinationUri(fileUri);
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">下载管理Id</span>
<span style="color: #000000;">                    downloadManager.enqueue(request);
                    DownloadReceiver mDownloaderReceiver </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> DownloadReceiver();
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">注册下载完成广播</span>
                    context.registerReceiver(mDownloaderReceiver, <span style="color: #0000ff;">new</span><span style="color: #000000;"> IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));

                } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (Exception e) {
                    e.printStackTrace();
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">注意:如果文件下载失败则 使用浏览器下载
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> downloadByWeb(context, apkUrl);</span>
<span style="color: #000000;">                }
            }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h5>　　　　2.2 通过广播监控下载完成,调至提示用户安装操作.</h5>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    　　　　<span style="color: #008000;">/*</span><span style="color: #008000;">*
             * 下载完成的广播
             </span><span style="color: #008000;">*/</span>
            <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> DownloadReceiver extends BroadcastReceiver {
                @Override
                </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onReceive(Context context, Intent intent) {
                    </span><span style="color: #0000ff;">if</span> (!<span style="color: #000000;">TextUtils.isEmpty(downloadUpdateApkFilePath)) {
                        installNormal(context, downloadUpdateApkFilePath);
                    }
                }
            }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h5>　　　　2.3 提示用户安装或者更新</h5>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　　　/*</span><span style="color: #008000;">*
         * 提示安装
         * @param context 上下文
         * @param apkPath apk下载完成在手机中的路径
         </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> installNormal(Context context, String apkPath) {
            Intent intent </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> Intent(Intent.ACTION_VIEW);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">版本在7.0以上是不能直接通过uri访问的</span>
            <span style="color: #0000ff;">if</span> (Build.VERSION.SDK_INT &gt;<span style="color: #000000;"> Build.VERSION_CODES.N) {
                File file </span>= (<span style="color: #0000ff;">new</span><span style="color: #000000;"> File(apkPath));
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> 由于没有在Activity环境下启动Activity,设置下面的标签</span>
<span style="color: #000000;">                intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                </span><span style="color: #008000;">//</span><span style="color: #008000;">参数1:上下文, 参数2:Provider主机地址 和配置文件中保持一致,参数3:共享的文件</span>
                Uri apkUri = FileProvider.getUriForFile(context, <span style="color: #800000;">"</span><span style="color: #800000;">com.xxxxx.fileprovider</span><span style="color: #800000;">"</span><span style="color: #000000;">, file);
                </span><span style="color: #008000;">//</span><span style="color: #008000;">添加这一句表示对目标应用临时授权该Uri所代表的文件</span>
<span style="color: #000000;">                intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
                intent.setDataAndType(apkUri, </span><span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.android.package-archive</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                intent.setDataAndType(Uri.fromFile(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> File(apkPath)),
                        </span><span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.android.package-archive</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            }
            context.startActivity(intent);
        }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>三. 补充</h2>
<p>　　MIME_MapTable是所有文件的后缀名所对应的MIME类型的一个String数组</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　　　　　　　//</span><span style="color: #008000;">{后缀名，MIME类型} </span>
            {<span style="color: #800000;">"</span><span style="color: #800000;">.3gp</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/3gpp</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.apk</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.android.package-archive</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.asf</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/x-ms-asf</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.avi</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/x-msvideo</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.bin</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/octet-stream</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.bmp</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">image/bmp</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.c</span><span style="color: #800000;">"</span>,  <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.class</span><span style="color: #800000;">"</span>,  <span style="color: #800000;">"</span><span style="color: #800000;">application/octet-stream</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.conf</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.cpp</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.doc</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/msword</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.docx</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.openxmlformats-officedocument.wordprocessingml.document</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.xls</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.ms-excel</span><span style="color: #800000;">"</span><span style="color: #000000;">},  
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.xlsx</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.openxmlformats-officedocument.spreadsheetml.sheet</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.exe</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/octet-stream</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.gif</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">image/gif</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.gtar</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">application/x-gtar</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.gz</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">application/x-gzip</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.h</span><span style="color: #800000;">"</span>,  <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.htm</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">text/html</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.html</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">text/html</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.jar</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/java-archive</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.java</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.jpeg</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">image/jpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.jpg</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">image/jpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.js</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">application/x-javascript</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.log</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m3u</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-mpegurl</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m4a</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/mp4a-latm</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m4b</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/mp4a-latm</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m4p</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/mp4a-latm</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m4u</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/vnd.mpegurl</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.m4v</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/x-m4v</span><span style="color: #800000;">"</span><span style="color: #000000;">},  
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mov</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/quicktime</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mp2</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mp3</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mp4</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpc</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.mpohun.certificate</span><span style="color: #800000;">"</span><span style="color: #000000;">},        
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpe</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">},   
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpeg</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">video/mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">},   
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpg</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">video/mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">},   
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpg4</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">video/mp4</span><span style="color: #800000;">"</span><span style="color: #000000;">},    
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.mpga</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">audio/mpeg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.msg</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.ms-outlook</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.ogg</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/ogg</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.pdf</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/pdf</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.png</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">image/png</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.pps</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.ms-powerpoint</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.ppt</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.ms-powerpoint</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.pptx</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.openxmlformats-officedocument.presentationml.presentation</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.prop</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.rc</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.rmvb</span><span style="color: #800000;">"</span>,   <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-pn-realaudio</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.rtf</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/rtf</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.sh</span><span style="color: #800000;">"</span>, <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.tar</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/x-tar</span><span style="color: #800000;">"</span><span style="color: #000000;">},    
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.tgz</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/x-compressed</span><span style="color: #800000;">"</span><span style="color: #000000;">},  
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.txt</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.wav</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-wav</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.wma</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-ms-wma</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.wmv</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">audio/x-ms-wmv</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.wps</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/vnd.ms-works</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.xml</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">text/plain</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.z</span><span style="color: #800000;">"</span>,  <span style="color: #800000;">"</span><span style="color: #800000;">application/x-compress</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">"</span><span style="color: #800000;">.zip</span><span style="color: #800000;">"</span>,    <span style="color: #800000;">"</span><span style="color: #800000;">application/x-zip-compressed</span><span style="color: #800000;">"</span><span style="color: #000000;">}, 
            {</span><span style="color: #800000;">""</span>,        <span style="color: #800000;">"</span><span style="color: #800000;">*/*</span><span style="color: #800000;">"</span>} </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>