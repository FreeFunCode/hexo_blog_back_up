---
title: Android 开源框架 ( 六 ) Volley --- Google的轻量级网络通信框架
date: 2018-08-01 07:36:18
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/Volley.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.Volley介绍 　　</h2>
<p>　　2013年Google I/O大会上推出的一个新的Android网络通信框架,目标是将HTTP的通信操作再进行简单化,除了简单易用之外，Volley在性能方面也进行了大幅度的调整，它的设计目标就是非常适合去进行数据量不大，但通信频繁的网络操作，而对于大数据量的网络操作，比如说下载文件等，Volley的表现就会非常糟糕。不过谷歌已经停止更新Volley了，现在OkHttp成为新一代网络通信的新宠。</p>
<h2>二.Volley使用</h2>
<h4>1.导入jar包</h4>
<p>如果只是用于学习,可以通过gradle引用&nbsp;compile 'com.mcxiaoke.volley:library:1.0.19'&nbsp;了解下.但是如果用于项目中,最好下载Google官方的jar包.</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_13119" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">compile </code><code class="csharp string">'com.mcxiaoke.volley:library:1.0.19'</code><code class="csharp plain">　　</code></div></div></td></tr></tbody></table></div></div>
</div>
<h4>2.授权</h4>
<div class="cnblogs_code">
<pre>&lt;uses-permission android:name=<span style="color: #800000;">"</span><span style="color: #800000;">android.permission.INTERNET</span><span style="color: #800000;">"</span> /&gt;</pre>
</div>
<h4>3.使用JsonObjectRequest get请求方式示例</h4>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>        <span style="color: #008000;">//</span><span style="color: #008000;">1.创建一个请求队列RequestQueue     </span>
        RequestQueue queue= Volley.newRequestQueue(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
        </span><span style="color: #008000;">//</span><span style="color: #008000;">2.创建StringRequest对象 </span>
        JsonObjectRequest request=<span style="color: #0000ff;">new</span> JsonObjectRequest(url, <span style="color: #0000ff;">new</span> Response.Listener&lt;JSONObject&gt;<span style="color: #000000;">() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onResponse(JSONObject jsonObject) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">接收返回数据</span>
                Log.e(<span style="color: #800000;">"</span><span style="color: #800000;">success</span><span style="color: #800000;">"</span><span style="color: #000000;">,jsonObject.toString());
            }
        }, </span><span style="color: #0000ff;">new</span><span style="color: #000000;"> Response.ErrorListener() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onErrorResponse(VolleyError volleyError) {

            }
        });
        </span><span style="color: #008000;">//</span><span style="color: #008000;">3.将请求对象添加到请求队列中</span>
        queue.add(request);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　同样还有一些其他请求:ClearCacheRequest,ImageRequest,JsonArrayRequest,JsonRequest,StringRequest 用法一样.</p>
<p>　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180731105814344-1488596964.jpg" alt=""></p>
<hr>
<p>&nbsp;</p>
<p> 　　　　　　<strong>1.ClearCacheRequest</strong> :A synthetic request used for clearing the cache. 用于清除缓存请求类<br>		　　　　　　<strong>2.ImageRequest</strong> :A canned request for getting an image at a given URL and calling back with a decoded Bitmap. 通过Url获取对应解码位图decoded BitMap 请求类<br>		　　　　　　<strong>3.JsonArrayRequest</strong> :A request for retrieving a {@link JSONArray} response body at a given URL. 通过Url返回一个{@ Link JSONArray}Json数组请求类<br>		　　　　　　<strong>4.JsonObjectRequest</strong> :A request for retrieving a {@link JSONObject} response body at a given URL, allowing for an optional {@link JSONObject} to be passed in as part of the request body. 可以通过Url返回一个{@link JSONObject},也可以使用{@link JSONObject}当做请求Request的传递参数.<br>		　　　　　　<strong>5.JsonRequest</strong> :A request for retrieving a T type response body at a given URL that also optionally sends along a JSON body in the request specified. 通过Url返回一个定义的泛型T ,也可以使用JSON当做请求Request的传递参数的类<br>		　　　　　　<strong>6.StringRequest</strong> :A canned request for retrieving the response body at a given URL as a String. 通过Url返回一个String字符串请求类</p>
<hr>
<h4><br>	　　4.使用JsonObjectRequest post请求方式示例</h4>
<p>	　　Post请求,只需要在Request请求里,添加一个参数Request.Method.POST : new JsonObjectRequest(Request.Method.POST,url, new Response.Listener&lt;JSONObject&gt;() {...});其他部分一样.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>RequestQueue queue= Volley.newRequestQueue(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
        JsonObjectRequest request</span>=<span style="color: #0000ff;">new</span> JsonObjectRequest(Request.Method.POST,url, <span style="color: #0000ff;">new</span> Response.Listener&lt;JSONObject&gt;<span style="color: #000000;">() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onResponse(JSONObject jsonObject) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">接收返回数据</span>
                Log.e(<span style="color: #800000;">"</span><span style="color: #800000;">success</span><span style="color: #800000;">"</span><span style="color: #000000;">,jsonObject.toString());
            }
        }, </span><span style="color: #0000ff;">new</span><span style="color: #000000;"> Response.ErrorListener() {
            @Override
            </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onErrorResponse(VolleyError volleyError) {

            }
        });
        queue.add(request);</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> 　　通过Request.Method.能看到Volley支持的一些请求方式: <br>	 	　　　　　　　　　　<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180731105752291-1794437800.jpg" alt=""><br>	</p>
<h4>　　5.HurlStack 与&nbsp;HttpClientStack 介绍</h4>
<p>　　&nbsp;Volley是官方出的，volley在设计的时候是将具体的请求客户端做了下封装，也就是说可以支持HttpUrlConnection, HttpClient.当然也可以自己封装支持OkHttp.</p>
<p>　　Volley.java类</p>
<p>　　</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">　　if</span> (stack == <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            </span><span style="color: #0000ff;">if</span> (Build.VERSION.SDK_INT &gt;= <span style="color: #800080;">9</span><span style="color: #000000;">) {
                </span><span style="color: #008000;">//</span><span style="color: #008000;">HttpURLConnection 请求方式</span>
                stack = <span style="color: #0000ff;">new</span><span style="color: #000000;"> HurlStack();
            } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Prior to Gingerbread, HttpUrlConnection was unreliable.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> See: </span><span style="color: #008000; text-decoration: underline;">http://android-developers.blogspot.com/2011/09/androids-http-clients.html</span>
                <span style="color: #008000;">//</span><span style="color: #008000;">HttpClient 请求方式</span>
                stack = <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpClientStack(AndroidHttpClient.newInstance(userAgent));
            }
        }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>然后具体看下&nbsp;HurlStack.java 里的HttpUrlConnection请求封装.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>　　/**
     *<span style="color: #000000;"> An {@link HttpStack} based on {@link HttpURLConnection}.
     </span>*/
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> HurlStack implements HttpStack {

    </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> final String HEADER_CONTENT_TYPE = <span style="color: #800000;">"</span><span style="color: #800000;">Content-Type</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    
    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * Create an {@link HttpURLConnection} for the specified {@code url}.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">protected</span><span style="color: #000000;"> HttpURLConnection createConnection(URL url) throws IOException {
        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> (HttpURLConnection) url.openConnection();
    }

    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * Opens an {@link HttpURLConnection} with parameters.
     * @param url
     * @return an open connection
     * @throws IOException
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">private</span> HttpURLConnection openConnection(URL url, Request&lt;?&gt;<span style="color: #000000;"> request) throws IOException {
        HttpURLConnection connection </span>=<span style="color: #000000;"> createConnection(url);

        </span><span style="color: #0000ff;">int</span> timeoutMs =<span style="color: #000000;"> request.getTimeoutMs();
        connection.setConnectTimeout(timeoutMs);
        connection.setReadTimeout(timeoutMs);
        connection.setUseCaches(</span><span style="color: #0000ff;">false</span><span style="color: #000000;">);
        connection.setDoInput(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> use caller-provided custom SslSocketFactory, if any, for HTTPS</span>
        <span style="color: #0000ff;">if</span> (<span style="color: #800000;">"</span><span style="color: #800000;">https</span><span style="color: #800000;">"</span>.equals(url.getProtocol()) &amp;&amp; mSslSocketFactory != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
            ((HttpsURLConnection)connection).setSSLSocketFactory(mSslSocketFactory);
        }

        </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> connection;
    }

    @SuppressWarnings(</span><span style="color: #800000;">"</span><span style="color: #800000;">deprecation</span><span style="color: #800000;">"</span><span style="color: #000000;">)
    </span><span style="color: #008000;">/*</span><span style="color: #008000;"> package </span><span style="color: #008000;">*/</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> setConnectionParametersForRequest(HttpURLConnection connection,
            Request</span>&lt;?&gt;<span style="color: #000000;"> request) throws IOException, AuthFailureError {
        </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (request.getMethod()) {
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.DEPRECATED_GET_OR_POST:
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> This is the deprecated way that needs to be handled for backwards compatibility.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the request's post body is null, then the assumption is that the request is
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> GET.  Otherwise, it is assumed that the request is a POST.</span>
                <span style="color: #0000ff;">byte</span>[] postBody =<span style="color: #000000;"> request.getPostBody();
                </span><span style="color: #0000ff;">if</span> (postBody != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Prepare output. There is no need to set Content-Length explicitly,
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> since this is handled by HttpURLConnection using the size of the prepared
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> output stream.</span>
                    connection.setDoOutput(<span style="color: #0000ff;">true</span><span style="color: #000000;">);
                    connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">POST</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                    connection.addRequestProperty(HEADER_CONTENT_TYPE,
                            request.getPostBodyContentType());
                    DataOutputStream </span><span style="color: #0000ff;">out</span> = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DataOutputStream(connection.getOutputStream());
                    </span><span style="color: #0000ff;">out</span><span style="color: #000000;">.write(postBody);
                    </span><span style="color: #0000ff;">out</span><span style="color: #000000;">.close();
                }
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.GET:
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> Not necessary to set the request method because connection defaults to GET but
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> being explicit here.</span>
                connection.setRequestMethod(<span style="color: #800000;">"</span><span style="color: #800000;">GET</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.DELETE:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">DELETE</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.POST:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">POST</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                addBodyIfExists(connection, request);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.PUT:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">PUT</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                addBodyIfExists(connection, request);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.HEAD:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">HEAD</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.OPTIONS:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">OPTIONS</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.TRACE:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">TRACE</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.PATCH:
                connection.setRequestMethod(</span><span style="color: #800000;">"</span><span style="color: #800000;">PATCH</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                addBodyIfExists(connection, request);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">default</span><span style="color: #000000;">:
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalStateException(<span style="color: #800000;">"</span><span style="color: #800000;">Unknown method type.</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        }
    }

        </span><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span> addBodyIfExists(HttpURLConnection connection, Request&lt;?&gt;<span style="color: #000000;"> request)
            throws IOException, AuthFailureError {
            </span><span style="color: #0000ff;">byte</span>[] body =<span style="color: #000000;"> request.getBody();
            </span><span style="color: #0000ff;">if</span> (body != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                connection.setDoOutput(</span><span style="color: #0000ff;">true</span><span style="color: #000000;">);
                connection.addRequestProperty(HEADER_CONTENT_TYPE, request.getBodyContentType());
                DataOutputStream </span><span style="color: #0000ff;">out</span> = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DataOutputStream(connection.getOutputStream());
                </span><span style="color: #0000ff;">out</span><span style="color: #000000;">.write(body);
                </span><span style="color: #0000ff;">out</span><span style="color: #000000;">.close();
            }
        }

}    </span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>在看看HttpClientStack.java 里的HttpClient的封装.&nbsp; 注意下哈,<strong>HttpClient 已经在Android 6.0 删除</strong>.</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">/*</span><span style="color: #008000;">*
 * An HttpStack that performs request over an {@link HttpClient}.
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> HttpClientStack implements HttpStack {
    </span><span style="color: #0000ff;">protected</span><span style="color: #000000;"> final HttpClient mClient;

    </span><span style="color: #0000ff;">private</span> final <span style="color: #0000ff;">static</span> String HEADER_CONTENT_TYPE = <span style="color: #800000;">"</span><span style="color: #800000;">Content-Type</span><span style="color: #800000;">"</span><span style="color: #000000;">;
    
    
    </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
     * Creates the appropriate subclass of HttpUriRequest for passed in request.
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    @SuppressWarnings(</span><span style="color: #800000;">"</span><span style="color: #800000;">deprecation</span><span style="color: #800000;">"</span><span style="color: #000000;">)
    </span><span style="color: #008000;">/*</span><span style="color: #008000;"> protected </span><span style="color: #008000;">*/</span> <span style="color: #0000ff;">static</span> HttpUriRequest createHttpRequest(Request&lt;?&gt;<span style="color: #000000;"> request,
            Map</span>&lt;String, String&gt;<span style="color: #000000;"> additionalHeaders) throws AuthFailureError {
        </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (request.getMethod()) {
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.DEPRECATED_GET_OR_POST: {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> This is the deprecated way that needs to be handled for backwards compatibility.
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> If the request's post body is null, then the assumption is that the request is
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> GET.  Otherwise, it is assumed that the request is a POST.</span>
                <span style="color: #0000ff;">byte</span>[] postBody =<span style="color: #000000;"> request.getPostBody();
                </span><span style="color: #0000ff;">if</span> (postBody != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    HttpPost postRequest </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpPost(request.getUrl());
                    postRequest.addHeader(HEADER_CONTENT_TYPE, request.getPostBodyContentType());
                    HttpEntity entity;
                    entity </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> ByteArrayEntity(postBody);
                    postRequest.setEntity(entity);
                    </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> postRequest;
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpGet(request.getUrl());
                }
            }
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.GET:
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpGet(request.getUrl());
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.DELETE:
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpDelete(request.getUrl());
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.POST: {
                HttpPost postRequest </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpPost(request.getUrl());
                postRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                setEntityIfNonEmptyBody(postRequest, request);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> postRequest;
            }
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.PUT: {
                HttpPut putRequest </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpPut(request.getUrl());
                putRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                setEntityIfNonEmptyBody(putRequest, request);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> putRequest;
            }
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.HEAD:
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpHead(request.getUrl());
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.OPTIONS:
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpOptions(request.getUrl());
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.TRACE:
                </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpTrace(request.getUrl());
            </span><span style="color: #0000ff;">case</span><span style="color: #000000;"> Method.PATCH: {
                HttpPatch patchRequest </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> HttpPatch(request.getUrl());
                patchRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                setEntityIfNonEmptyBody(patchRequest, request);
                </span><span style="color: #0000ff;">return</span><span style="color: #000000;"> patchRequest;
            }
            </span><span style="color: #0000ff;">default</span><span style="color: #000000;">:
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalStateException(<span style="color: #800000;">"</span><span style="color: #800000;">Unknown request method.</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        }
    }
    
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
</div>