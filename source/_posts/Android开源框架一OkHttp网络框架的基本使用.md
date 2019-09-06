---
title: Android 开源框架 ( 一 ) OkHttp 网络框架的基本使用
date: 2018-07-28 14:21:25
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/rxjava_get.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>&nbsp; &nbsp; &nbsp; &nbsp;<strong>HttpClient&nbsp; 和&nbsp;HttpURLConnection 以及 OkHttp</strong> :</p>
<p>　　在Android 2.2版本之前，HttpClient拥有较少的bug，因此使用它是最好的选择。 <br>&nbsp; &nbsp; &nbsp; &nbsp; 而在Android2.3版本及以后，HttpURLConnection则是最佳的选择。它的API简单，体积较小，因而非常适用于Android项目。压缩和缓存机制可以有效地减少网络访问的流量，在提升速度和省电方面也起到了较大的作用。对于新的应用程序应该更加偏向于使用HttpURLConnection，因为在以后的工作当中我们也会将更多的时间放在优化HttpURLConnection上面。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; 其实现在嘛，两者都不用，就用Okhttp. HttpUrlConnection现在的底层实现就是通过Okhttp.</p>
<h2>一. HttpUrlConnect 介绍</h2>
<h4>　　1. 网络通信首先需要配置网络请求权限:</h4>
<div class="cnblogs_code">
<pre>&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.INTERNET</span><span style="color: #800000;">"</span> /&gt;</pre>
</div>
<h4>　　2.使用如下方式实现子线程里http请求通信:&nbsp;</h4>
<p>　　开启线程两种常用的实现方式:一种是继承Thread类.一种是实现Runnable接口.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">new</span> Thread(<span style="color: #0000ff;">new</span><span style="color: #000000;"> Runnable() {

            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> run() {
                ...
                </span><span style="color: #008000;">//</span><span style="color: #008000;">子线程方法异步执行</span>
<span style="color: #000000;">                ...
            }
        }).start();</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　如果在主线程(也就是我们常说的UI线程)中进行Http请求,会报如下错.(NetworkOnMainThreadException)</p>
<div class="cnblogs_code">
<pre>java.lang.RuntimeException: Unable to start activity ComponentInfo{com.android.okhttp.okhttp/<span style="color: #000000;">com.android.okhttp.okhttp.MainActivity}: android.os.NetworkOnMainThreadException
        at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:</span><span style="color: #800080;">2855</span>)</pre>
</div>
<h4>　　3.使用HttpUrlConnect实现异步http get请求获取网络数据示例:</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> 1. 得到访问地址的URL</span>
            URL url = <span style="color: #0000ff;">new</span><span style="color: #000000;"> URL(urlStr);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 2. 得到网络访问对象java.net.HttpURLConnection</span>
            HttpURLConnection connection =<span style="color: #000000;"> (HttpURLConnection) url.openConnection();
            </span><span style="color: #008000;">/*</span><span style="color: #008000;"> 3. 设置请求参数（过期时间，输入、输出流、访问方式），以流的形式进行连接 </span><span style="color: #008000;">*/</span>
            <span style="color: #008000;">//</span><span style="color: #008000;"> 设置是否向HttpURLConnection输出</span>
            connection.setDoOutput(<span style="color: #0000ff;">false</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置是否从httpUrlConnection读入</span>
            connection.setDoInput(<span style="color: #0000ff;">true</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置请求方式</span>
            connection.setRequestMethod(<span style="color: #800000;">"</span><span style="color: #800000;">GET</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置是否使用缓存</span>
            connection.setUseCaches(<span style="color: #0000ff;">true</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置此 HttpURLConnection 实例是否应该自动执行 HTTP 重定向</span>
            connection.setInstanceFollowRedirects(<span style="color: #0000ff;">true</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 设置超时时间</span>
            connection.setConnectTimeout(<span style="color: #800080;">3000</span><span style="color: #000000;">);
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 连接</span>
<span style="color: #000000;">            connection.connect();
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 4. 得到响应状态码的返回值 responseCode</span>
            <span style="color: #0000ff;">int</span> code =<span style="color: #000000;"> connection.getResponseCode();
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 5. 如果返回值正常，数据在网络中是以流的形式得到服务端返回的数据</span>
            String msg = <span style="color: #800000;">""</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">if</span> (code == <span style="color: #800080;">200</span>) { <span style="color: #008000;">//</span><span style="color: #008000;"> 正常响应
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> 从流中读取响应信息</span>
                BufferedReader reader = <span style="color: #0000ff;">new</span><span style="color: #000000;"> BufferedReader(
                        </span><span style="color: #0000ff;">new</span><span style="color: #000000;"> InputStreamReader(connection.getInputStream()));
                String line </span>= <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> 循环从流中读取</span>
                <span style="color: #0000ff;">while</span> ((line = reader.readLine()) != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    msg </span>+= line + <span style="color: #800000;">"</span><span style="color: #800000;">\n</span><span style="color: #800000;">"</span><span style="color: #000000;">;
                }
                reader.close(); </span><span style="color: #008000;">//</span><span style="color: #008000;"> 关闭流</span>
<span style="color: #000000;">            }
            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 6. 断开连接，释放资源</span>
<span style="color: #000000;">            connection.disconnect();

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> 显示响应结果</span>
            Log.i(<span style="color: #800000;">"</span><span style="color: #800000;">getDataByHttpUrlConnect</span><span style="color: #800000;">"</span><span style="color: #000000;">,msg);
            </span><span style="color: #008000;">//</span><span style="color: #008000;">组装消息</span>
            Message message = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Message();
            message.what </span>=<span style="color: #000000;"> RESPONSE_OK;
            message.obj </span>=<span style="color: #000000;"> msg;
            </span><span style="color: #008000;">//</span><span style="color: #008000;">发送消息</span>
<span style="color: #000000;">            handler.sendMessage(message);<br></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>　　　　4.在子线程进行网络请求后,将返回的信息在主线程控件上刷新显示. 使用到Handler操作.</h4>
<pre><span>　　//定义一个Handler来接收子线程发送的消息并处理:</span></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">            Handler handler </span>= <span style="color: #0000ff;">new</span> Handler(<span style="color: #0000ff;">new</span><span style="color: #000000;"> Handler.Callback() {
                @Override
                </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> boolean handleMessage(Message message) {
                    </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (message.what){
                        </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> RESPONSE_OK:
                            </span><span style="color: #008000;">//</span><span style="color: #008000;">将返回的消息打印在界面TextView控件上显示</span>
<span style="color: #000000;">                            tv_response.setText(message.obj.toString());
                            </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
                    }
                    </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">true</span><span style="color: #000000;">;
                }
            });</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h2>二. OkHttp 介绍</h2>
<p>　　<strong>Requests 和&nbsp;Responses</strong> 两个属性概念解释:</p>
<p>　　　　Requests（请求） 每一个HTTP请求中都应该包含一个URL，一个GET或POST方法以及Header或其他参数，当然还可以含特定内容类型的数据流。<br>					　　　　　　　　注意下Content-Type:<br>					　　　　　　　　　　RequestBody的数据格式都要指定Content-Type，常见的有三种：<br>					　　　　　　　　　　application/x-www-form-urlencoded 数据是个普通表单<br>					　　　　　　　　　　multipart/form-data 数据里有文件<br>					　　　　　　　　　　application/json 数据是个json<br>					<br>	　　　　Responses（响应） 响应则包含一个回复代码（200代表成功，404代表未找到等灯），Header和定制可选的body。</p>
<h4>　　1.项目中配置使用OkHttp　　</h4>
<p>　　首先gradle 引入类库(AndroidStudio显示最新的是3.11.0版本-2018年7月28日):</p>
<div class="cnblogs_code">
<pre>implementation <span style="color: #800000;">'</span><span style="color: #800000;">com.squareup.okhttp3:okhttp:3.11.0</span><span style="color: #800000;">'</span>    </pre>
</div>
<p> 　　或者下载jar包:<br>	　　　　OkHttp官网地址：http://square.github.io/okhttp/<br>    　　　　OkHttp GitHub地址：https://github.com/square/okhttp</p>
<h4>　　2. OkHttp get请求示例:</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　　　　　//</span><span style="color: #008000;">new OkHttpClient</span>
            OkHttpClient okHttpClient = <span style="color: #0000ff;">new</span><span style="color: #000000;"> OkHttpClient();
                </span><span style="color: #008000;">//</span><span style="color: #008000;">封装Request请求信息,get方式,请求url</span>
                Request request = <span style="color: #0000ff;">new</span> Request.Builder().<span style="color: #0000ff;">get</span><span style="color: #000000;">().url(urlStr).build();
                </span><span style="color: #008000;">//</span><span style="color: #008000;">定义一个接受返回信息的Response</span>
                Response response = <span style="color: #0000ff;">null</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">try</span><span style="color: #000000;"> {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;">调用OkHttp内部真正的实现请求,并返回请求结果Response</span>
                    response =<span style="color: #000000;"> okHttpClient.newCall(request).execute();
                    </span><span style="color: #0000ff;">if</span><span style="color: #000000;">(response.isSuccessful()){
                        </span><span style="color: #008000;">//</span><span style="color: #008000;">请求回来不能再子线程里进行界面Ui更新操作,通过Handler来通知主线程界面刷新</span>
                        Message message = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Message();
                        message.what </span>=<span style="color: #000000;"> RESPONSE_OK;
                        message.obj </span>=<span style="color: #000000;"> response.body().toString();
                        handler.sendMessage(message);
                    }
                } </span><span style="color: #0000ff;">catch</span><span style="color: #000000;"> (IOException e) {
                    e.printStackTrace();
                }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>