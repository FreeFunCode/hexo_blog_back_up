---
title: AIDL介绍以及简单使用
date: 2018-07-21 13:05:21
tags: AIDL
categories: 
- Android
- 知识点
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/aidl.html)

<div id="cnblogs_post_body" class="blogpost-body cnblogs-markdown">
    <div class="toc">
    <p class="toc-title">目录</p>
    <div class="toc-list">
        <ul>
        <li><a href="#一.-aidl-介绍.">一. AIDL 介绍.</a></li>
        <li><a href="#二.-aidl简单应用.">二. AIDL简单应用.</a><ul>
        <li><a href="#aidl_server端">Aidl_Server端</a></li>
        </ul></li>
        <li><a href="#section"></a><ul>
        <li><a href="#aidl_client端">Aidl_Client端</a></li>
        </ul></li>
        <li><a href="#三.注意事项">三.注意事项</a></li>
        </ul>
    </div>
</div>
<h2 id="一.-aidl-介绍.">一. AIDL 介绍.</h2>
<pre><code class="hljs"> AIDL（Android接口描述语言）是一个IDL语言，它可以生成一段代码，可以是一个在Android设备上运行的两个进程使用内部通信进程进行交互。</code></pre>
<p>如果你想在一个进程中（例如在一个Activity中）访问另一个进程中（例如service）某个对象的方法，你就可以使用AIDL来生成这样的代码来伪装传递各种参数。</p>
<p>稍微了解下几个常见的名词(IPC,匿名共享内存,Binder,Aidl,消息,广播,ContentProvider,Intent):<br>
<strong>IPC是一种概念，即进程间通信；其它几个都是Android里的概念；</strong><br>
<strong>匿名共享内存(Anonymous Shared Memory):其作用之一即通过Binder进程间通信机制来实现进程间的内存共享。 </strong><br>
<strong>Binder:Binder是对IPC的具体实行，是IPC的一种具体实现.其本质也是调用系统底层的共享内存实现.</strong><br>
<strong>AIDL:进程间的通信，速度快(系统底层直接是共享内存)，性能稳，效率高，一般进程间通信就用它. AIDL是Binder机制向外提供的接口，目的就是为了方便对Binder的使用；</strong><br>
<strong>消息(Messager)：Messenger本质也是AIDL，只是进行了封装，开发的时候不用再写.aidl文件,效率应该是和Aidl是一样的，与Aidl的区别在于Messager是线程安全的，而Aidl是非线程安全的，所以Aidl在使用的时候应该注意这个问题;</strong><br>
<strong>广播(BroadcastReceiver):只要注册了广播，都能收到，有点范围广，缺点速度慢必须在一定时间完成处理操作,同其他Android四大组件一样,都不能执行耗时操作;</strong><br>
<strong>ContentProvider:暴露app的数据访问接口，让其他应该访问app数据.同时也能通过ContentProvider来访问第三方的一些app数据(如通讯录,日历等暴露接口的应用);</strong><br>
<strong>Intent:Intent是最高层级的封装，实质是封装了对Binder的使用，当然Intent也常常在同一进程中调用，只是把两种方式封装在一起了.</strong><br>
<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721125726338-2042877965.jpg"></p>
<h2 id="二.-aidl简单应用.">二. AIDL简单应用.</h2>
<pre><code class="hljs vbscript">编写两个app应用程序,一个实现Aidl传递的<span class="hljs-built_in">Server</span>端,一个实现Aidl的Client端:
aidl的<span class="hljs-built_in">Server</span>端,接收Client端传过来的两个<span class="hljs-built_in">int</span>值,定义一个加法计算的逻辑规则, 并返回 两个<span class="hljs-built_in">int</span>数之和, return num1 + num2;
aidl的Client端,从界面用户输入两个<span class="hljs-built_in">int</span>数,点击绑定<span class="hljs-built_in">Server</span>端服务,点击计算,调用<span class="hljs-built_in">Server</span>端加法逻辑,将返回结果 输入到用户界面展示,两数之和.</code></pre>
<h3 id="aidl_server端">Aidl_Server端</h3>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721125835612-1721114769.jpg"></p>
<pre class="java"><code class="hljs"><span class="hljs-comment">// IImoocAIDL.aidl</span>
 <span class="hljs-keyword">package</span> com.example.administrator.aidl_server.service;

<span class="hljs-comment">// Declare any non-default types here with import statements</span>

<span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">IImoocAIDL</span> </span>{
     <span class="hljs-comment">//计算num1 + num2</span>
        <span class="hljs-function"><span class="hljs-keyword">int</span> <span class="hljs-title">add</span><span class="hljs-params">(<span class="hljs-keyword">int</span> num1,<span class="hljs-keyword">int</span> num2)</span></span>;
}</code></pre>
<p>编译后会生成</p>
<pre class="java"><code class="hljs"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">IImoocAIDL</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">android</span>.<span class="hljs-title">os</span>.<span class="hljs-title">IInterface</span> </span>{
    <span class="hljs-comment">/**
     * Local-side IPC implementation stub class.
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">abstract</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Stub</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">android</span>.<span class="hljs-title">os</span>.<span class="hljs-title">Binder</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">com</span>.<span class="hljs-title">example</span>.<span class="hljs-title">administrator</span>.<span class="hljs-title">aidl_server</span>.<span class="hljs-title">service</span>.<span class="hljs-title">IImoocAIDL</span> </span>{
            ...
            <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Proxy</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">com</span>.<span class="hljs-title">example</span>.<span class="hljs-title">administrator</span>.<span class="hljs-title">aidl_server</span>.<span class="hljs-title">service</span>.<span class="hljs-title">IImoocAIDL</span> </span>{
            <span class="hljs-keyword">private</span> android.os.IBinder mRemote;

            Proxy(android.os.IBinder remote) {
                mRemote = remote;
            }

            <span class="hljs-meta">@Override</span>
            <span class="hljs-keyword">public</span> android.os.<span class="hljs-function">IBinder <span class="hljs-title">asBinder</span><span class="hljs-params">()</span> </span>{
                <span class="hljs-keyword">return</span> mRemote;
            }

            <span class="hljs-keyword">public</span> java.lang.<span class="hljs-function">String <span class="hljs-title">getInterfaceDescriptor</span><span class="hljs-params">()</span> </span>{
                <span class="hljs-keyword">return</span> DESCRIPTOR;
            }

            <span class="hljs-meta">@Override</span>
            <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">add</span><span class="hljs-params">(<span class="hljs-keyword">int</span> num1, <span class="hljs-keyword">int</span> num2)</span> <span class="hljs-keyword">throws</span> android.os.RemoteException </span>{
                ...
            }
            
            ...
        }
            ...
    }
}</code></pre>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721125949447-88355980.jpg"></p>
<p>//om.example.administrator.aidl_server.IRemoteService</p>
<pre class="java"><code class="hljs"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">IRemoteService</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Service</span> </span>{
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String TAG = <span class="hljs-string">"IRemoteService"</span>;
    <span class="hljs-comment">//客户端绑定service时会执行</span>
    <span class="hljs-meta">@Override</span>
    <span class="hljs-function"><span class="hljs-keyword">public</span> IBinder <span class="hljs-title">onBind</span><span class="hljs-params">(Intent intent)</span> </span>{
        <span class="hljs-keyword">return</span> iBinder;
    }

    <span class="hljs-keyword">private</span> IBinder iBinder = <span class="hljs-keyword">new</span> IImoocAIDL.Stub() {
        <span class="hljs-meta">@Override</span>
        <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">add</span><span class="hljs-params">(<span class="hljs-keyword">int</span> num1, <span class="hljs-keyword">int</span> num2)</span> <span class="hljs-keyword">throws</span> RemoteException </span>{
            Log.e(TAG,<span class="hljs-string">"收到了来自客户端的请求"</span> + num1 + <span class="hljs-string">"+"</span> + num2 );
            <span class="hljs-keyword">return</span> num1 + num2;
        }

    };
}</code></pre>
<p>//AndroidManifest.xml</p>
<pre class="xml"><code class="hljs"><span class="hljs-tag">&lt;<span class="hljs-name">application</span>
    <span class="hljs-attr">...</span>
        &lt;<span class="hljs-attr">service</span> <span class="hljs-attr">android:name</span>=<span class="hljs-string">".IRemoteService"</span>
            <span class="hljs-attr">android:process</span>=<span class="hljs-string">":remote"</span>
            <span class="hljs-attr">android:exported</span>=<span class="hljs-string">"true"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-name">intent-filter</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-name">action</span> <span class="hljs-attr">android:name</span>=<span class="hljs-string">"com.example.administrator.aidl_server"</span>/&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-name">intent-filter</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-name">service</span>&gt;</span>
    ...
<span class="hljs-tag">&lt;/<span class="hljs-name">application</span>&gt;</span></code></pre>
<h2 id="section"></h2>
<h3 id="aidl_client端">Aidl_Client端</h3>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721125848817-895261162.jpg"></p>
<pre class="java"><code class="hljs">com.example.administrator.aidl_client.MainActivity
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MainActivity</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">AppCompatActivity</span> </span>{
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String TAG = <span class="hljs-string">"MainActivity"</span>;
    <span class="hljs-keyword">private</span> EditText et_send_input,nub_1,nub_2;
    <span class="hljs-keyword">private</span> TextView tv_send;
    <span class="hljs-keyword">private</span> TextView tv_bind;
    <span class="hljs-keyword">private</span> IImoocAIDL iImoocAIDLService;

    <span class="hljs-keyword">private</span> ServiceConnection conn = <span class="hljs-keyword">new</span> ServiceConnection() {

        <span class="hljs-comment">//绑定服务，回调onBind()方法</span>
        <span class="hljs-meta">@Override</span>
        <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onServiceConnected</span><span class="hljs-params">(ComponentName name, IBinder service)</span> </span>{
            iImoocAIDLService = IImoocAIDL.Stub.asInterface(service);
        }

        <span class="hljs-meta">@Override</span>
        <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onServiceDisconnected</span><span class="hljs-params">(ComponentName name)</span> </span>{
            iImoocAIDLService = <span class="hljs-keyword">null</span>;
        }
    };


    <span class="hljs-meta">@Override</span>
    <span class="hljs-function"><span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onCreate</span><span class="hljs-params">(Bundle savedInstanceState)</span> </span>{
        <span class="hljs-keyword">super</span>.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        bindService();

        et_send_input = findViewById(R.id.et_send_input);
        nub_1 = findViewById(R.id.nub_1);
        nub_2 = findViewById(R.id.nub_2);
        tv_send = findViewById(R.id.tv_send);
        tv_bind = findViewById(R.id.tv_bind);

        tv_send.setOnClickListener(<span class="hljs-keyword">new</span> View.OnClickListener() {
            <span class="hljs-meta">@Override</span>
            <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onClick</span><span class="hljs-params">(View view)</span> </span>{
                <span class="hljs-keyword">try</span> {
                    <span class="hljs-keyword">int</span> nub1 = Integer.parseInt(nub_1.getText().toString().trim());
                    <span class="hljs-keyword">int</span> nub2 = Integer.parseInt(nub_2.getText().toString().trim());

                    <span class="hljs-keyword">int</span> result = iImoocAIDLService.add(nub1,nub2);
                    et_send_input.setText(result+<span class="hljs-string">""</span>);
                }<span class="hljs-keyword">catch</span> (Exception e){
                    e.printStackTrace();
                }

            }
        });

        tv_bind.setOnClickListener(<span class="hljs-keyword">new</span> View.OnClickListener() {
            <span class="hljs-meta">@Override</span>
            <span class="hljs-function"><span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onClick</span><span class="hljs-params">(View view)</span> </span>{
                bindService();
                Log.e(TAG,<span class="hljs-string">"bindService"</span>+iImoocAIDLService);
            }
        });
    }

    <span class="hljs-function"><span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">bindService</span><span class="hljs-params">()</span> </span>{
        Intent intent = <span class="hljs-keyword">new</span> Intent();
        <span class="hljs-comment">//绑定服务端的service</span>
        intent.setAction(<span class="hljs-string">"com.example.administrator.aidl_server.IRemoteService"</span>);
        <span class="hljs-comment">//新版本（5.0后）必须显式intent启动 绑定服务</span>
        intent.setComponent(<span class="hljs-keyword">new</span> ComponentName(<span class="hljs-string">"com.example.administrator.aidl_server"</span>,<span class="hljs-string">"com.example.administrator.aidl_server.IRemoteService"</span>));
        <span class="hljs-comment">//绑定的时候服务端自动创建</span>
        bindService(intent,conn, Context.BIND_AUTO_CREATE);
    }

    <span class="hljs-meta">@Override</span>
    <span class="hljs-function"><span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onDestroy</span><span class="hljs-params">()</span> </span>{
        <span class="hljs-keyword">super</span>.onDestroy();
        unbindService(conn);
    }
}</code></pre>
<p>layout/activity_main.xml</p>
<pre class="xml"><code class="hljs"><span class="php"><span class="hljs-meta">&lt;?</span>xml version=<span class="hljs-string">"1.0"</span> encoding=<span class="hljs-string">"utf-8"</span><span class="hljs-meta">?&gt;</span></span>
<span class="hljs-tag">&lt;<span class="hljs-name">LinearLayout</span> <span class="hljs-attr">xmlns:android</span>=<span class="hljs-string">"http://schemas.android.com/apk/res/android"</span>
    <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
    <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"match_parent"</span>
    <span class="hljs-attr">android:orientation</span>=<span class="hljs-string">"vertical"</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-name">LinearLayout</span>
        <span class="hljs-attr">android:gravity</span>=<span class="hljs-string">"center"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"match_parent"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-name">EditText</span>
            <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/nub_1"</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:inputType</span>=<span class="hljs-string">"number"</span> /&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-name">TextView</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:text</span>=<span class="hljs-string">"+"</span> /&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-name">EditText</span>
            <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/nub_2"</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:inputType</span>=<span class="hljs-string">"number"</span> /&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-name">TextView</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:text</span>=<span class="hljs-string">"="</span> /&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-name">EditText</span>
            <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/et_send_input"</span>
            <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"wrap_content"</span>
            <span class="hljs-attr">android:inputType</span>=<span class="hljs-string">"number"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-name">LinearLayout</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-name">TextView</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/tv_bind"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"200dp"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"55dp"</span>
        <span class="hljs-attr">android:layout_gravity</span>=<span class="hljs-string">"center"</span>
        <span class="hljs-attr">android:layout_marginBottom</span>=<span class="hljs-string">"15dp"</span>
        <span class="hljs-attr">android:background</span>=<span class="hljs-string">"@color/colorAccent"</span>
        <span class="hljs-attr">android:gravity</span>=<span class="hljs-string">"center"</span>
        <span class="hljs-attr">android:text</span>=<span class="hljs-string">"bind"</span> /&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-name">TextView</span>
        <span class="hljs-attr">android:id</span>=<span class="hljs-string">"@+id/tv_send"</span>
        <span class="hljs-attr">android:layout_width</span>=<span class="hljs-string">"200dp"</span>
        <span class="hljs-attr">android:layout_height</span>=<span class="hljs-string">"55dp"</span>
        <span class="hljs-attr">android:layout_gravity</span>=<span class="hljs-string">"center"</span>
        <span class="hljs-attr">android:background</span>=<span class="hljs-string">"@color/colorAccent"</span>
        <span class="hljs-attr">android:gravity</span>=<span class="hljs-string">"center"</span>
        <span class="hljs-attr">android:text</span>=<span class="hljs-string">"计算"</span> /&gt;</span>


<span class="hljs-tag">&lt;/<span class="hljs-name">LinearLayout</span>&gt;</span></code></pre>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721125924545-1098549099.jpg"></p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180721130234841-1703766935.jpg"></p>
<h2 id="三.注意事项">三.注意事项</h2>
<p>如果运行中报错:</p>
<h4 id="aidl-method-threw-java.lang.securityexception-exception.">aidl Method threw 'java.lang.SecurityException' exception.</h4>
<p>需要 确保客户端的aidl文件包名要与服务端的包名一样.也就是本项目中的IImoocAIDL.aidl文件包名一致.</p>
<hr>
<p>[1] 参考资料: https://blog.csdn.net/jinxinliu1/article/details/70174591</p>
</div>