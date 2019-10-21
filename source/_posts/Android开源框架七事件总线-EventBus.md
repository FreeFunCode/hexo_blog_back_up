---
title: Android 开源框架 ( 七 ) 事件总线---EventBus
date: 2018-08-02 22:54:14
tags: 开源框架
categories: 
- Android
- 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/eventbus.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　</p>
<h2 style="display: inline !important;">一.引言</h2>
<div><em id="__mceDel">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;</em>与四大组件之一的BroadCast广播比较，广播主要监听系统级事件，比如网络切换，电池电量等属于进程间的通信，EventBus 是进程内的通信。<br>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp;了解BroadCast 可以查看该文章：<a id="post_title_link_9045362" href="https://www.cnblogs.com/bugzone/p/BroadcastReceiver.html" target="_blank">Android 四大组件 (三) BroadcastReceiver 介绍</a></div>
<h2>二.基本使用</h2>
<div>　　引入类库:
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.jakewharton:butterknife:8.5.1</span><span style="color: #800000;">'</span></pre>
</div>
<p>　　页面打开时候初始化并注册EventBus</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">初始化</span>
<span style="color: #000000;">
 @Override
    </span><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main); 
  　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;">注册 EventBus</span>
  　　　　EventBus.getDefault().register(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
 }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　页面销毁时候解除EventBus注册</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">销毁 EventBus</span>
<span style="color: #000000;"> @Override
 </span><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onDestroy() {
  super.onDestroy();
  EventBus.getDefault().unregister(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
 }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　发布事件 publish</p>
<div class="cnblogs_code">
<pre><span style="color: #008000;">//</span><span style="color: #008000;">发布事件</span>
 EventBus.getDefault().post(<span style="color: #0000ff;">new</span> EventBean(<span style="color: #800000;">"</span><span style="color: #800000;">我来自第二个页面的触发</span><span style="color: #800000;">"</span>));</pre>
</div>
<p>　　接受者 subscribe.注意方法名可以随便取,但是参数类型与publish发布者的保持一致. 这里是EventBean</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;">订阅者事件，处理事件反馈的消息</span>
<span style="color: #000000;"> @Subscribe
 </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> OnEvent(EventBean bean) {
  </span><span style="color: #008000;">//</span><span style="color: #008000;">拿到发布者传过来的event事件带的消息</span>
  String msgStr =<span style="color: #000000;"> bean.getMsg();
  </span><span style="color: #008000;">//</span><span style="color: #008000;">UI打印出拿到的消息</span>
<span style="color: #000000;">  tvFirst.setText(msgStr);
 }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<h2>三.ThreadMode 分析</h2>
<h3>　　五种 ThreadMode介绍　</h3>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_548763" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">　　　　ThreadMode.MAIN 订阅者将在Android的主线程（有时称为UI线程）中被调用。如果发布线程是主线程，则将直接调用事件处理程序方法（与ThreadMode.POSTING中描述的同步）。使用此模式的事件处理程序必须快速返回以避免阻塞主线程.</code></div><div class="line number2 index1 alt1"><code class="csharp plain">　　　　ThreadMode.MAIN_ORDERED 订阅者将在Android的主线程中被调用。该事件总是排队等待以后传递给订阅者。这给事件处理一个更严格和更一致的顺序（因此名字MAIN_ORDERED）。例如，如果使用MAIN线程模式在事件处理程序中发布另一个事件，</code></div><div class="line number3 index2 alt2"><code class="csharp plain">则第二个事件处理程序将在第一个事件处理程序之前完成（因为它被同步调用 - 将其与方法调用进行比较）。使用MAIN_ORDERED，第一个事件处理程序将完成，然后第二个将在稍后的时间点被调用（只要主线程有能力）.</code></div><div class="line number4 index3 alt1"><code class="csharp plain">　　　　ThreadMode.BACKGROUND 订阅者将在后台线程中被调用。如果发布线程不是主线程，则会在发布线程中直接调用事件处理程序方法。如果发布线程是主线程，则EventBus将使用一个后台线程来按顺序发送所有事件。使用这种模式的事件处理程序应该尽快返回以避免阻塞后台线程。</code></div><div class="line number5 index4 alt2"><code class="csharp plain">　　　　ThreadMode.ASYNC 事件处理程序方法在单独的线程中调用。这总是独立于发布线程和主线程。发布事件永远不会等待使用此模式的事件处理程序方法。事件处理程序方法应该使用这种模式，如果它们的执行可能需要一些时间，</code></div><div class="line number6 index5 alt1"><code class="csharp plain">例如网络访问。避免同时触发大量长时间运行的异步处理程序方法来限制并发线程的数量。EventBus使用线程池有效地重用已完成异步事件处理程序通知中的线程。</code></div><div class="line number7 index6 alt2"><code class="csharp plain">　　　　ThreadMode.POSTING这是默认的。订阅者将在发布该事件的同一个线程中被调用。事件传递是同步完成的，所有订阅者一旦发布完成就被调用。这个ThreadMode意味着最小的开销，因为它避免了完全的线程切换。</code></div><div class="line number8 index7 alt1"><code class="csharp plain">因此，对于已知完成的简单任务而言，这是推荐的模式，其时间很短，不需要主线程。使用这种模式的事件处理程序应该快速返回以避免阻塞发布线程，这可能是主线程。</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3>　　源码查看下五种ThreadMode不同处理方式:</h3>
</div>
<div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span> postToSubscription(Subscription subscription, Object <span style="color: #0000ff;">event</span><span style="color: #000000;">, boolean isMainThread) {
        </span><span style="color: #0000ff;">switch</span><span style="color: #000000;"> (subscription.subscriberMethod.threadMode) {
   　　　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;">发送和订阅者在同一线程内</span>
            <span style="color: #0000ff;">case</span><span style="color: #000000;"> POSTING:
                invokeSubscriber(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
   　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;">主线程</span>
            <span style="color: #0000ff;">case</span><span style="color: #000000;"> MAIN:
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (isMainThread) {
                    invokeSubscriber(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    mainThreadPoster.enqueue(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                }
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
   　　　　　　</span><span style="color: #008000;">//</span> 
            <span style="color: #0000ff;">case</span><span style="color: #000000;"> MAIN_ORDERED:
                </span><span style="color: #0000ff;">if</span> (mainThreadPoster != <span style="color: #0000ff;">null</span><span style="color: #000000;">) {
                    mainThreadPoster.enqueue(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    </span><span style="color: #008000;">//</span><span style="color: #008000;"> temporary: technically not correct as poster not decoupled from subscriber</span>
                    invokeSubscriber(subscription, <span style="color: #0000ff;">event</span><span style="color: #000000;">);
                }
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
   　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;">后台，不能并发处理</span>
            <span style="color: #0000ff;">case</span><span style="color: #000000;"> BACKGROUND:
                </span><span style="color: #0000ff;">if</span><span style="color: #000000;"> (isMainThread) {
                    backgroundPoster.enqueue(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                } </span><span style="color: #0000ff;">else</span><span style="color: #000000;"> {
                    invokeSubscriber(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                }
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
   　　　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;">子线程 异步并发处理 </span>
            <span style="color: #0000ff;">case</span><span style="color: #000000;"> ASYNC:
                asyncPoster.enqueue(subscription, </span><span style="color: #0000ff;">event</span><span style="color: #000000;">);
                </span><span style="color: #0000ff;">break</span><span style="color: #000000;">;
            </span><span style="color: #0000ff;">default</span><span style="color: #000000;">:
                </span><span style="color: #0000ff;">throw</span> <span style="color: #0000ff;">new</span> IllegalStateException(<span style="color: #800000;">"</span><span style="color: #800000;">Unknown thread mode: </span><span style="color: #800000;">"</span> +<span style="color: #000000;"> subscription.subscriberMethod.threadMode);
        }
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
&nbsp;
<h2>四.粘性事件</h2>
&nbsp;　　简单说就是: 发送事件之后再订阅该事件也能收到该事件。同样Android 中也有同样的粘性事件机制,如 粘性广播(Sticky Broadcast).</div>
<h3>&nbsp;　　粘性事件使用场景:</h3>
<p>　　一些事件进行信息感兴趣的事件后发布。 例如,一个事件信号,一些初始化完成。 或者如果你有传感器位置数据和你想抓住最近的值。 而不是实现自己的缓存,您可以使用黏性的事件。 EventBus保持过去的事件的特定类型在内存中。 黏性的事件可以交付给用户或显式查询。 因此,你不需要任何特殊的逻辑来考虑可用的数据。</p>
<div>
<h3>&nbsp;　　粘性使用示例：</h3>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> 1创建一个粘性事件类</span>
 <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> StickyEvent {
  </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> String msg;
  </span><span style="color: #0000ff;">public</span><span style="color: #000000;"> StickyEvent(String msg) {
   </span><span style="color: #0000ff;">this</span>.msg =<span style="color: #000000;"> msg;
  }
 }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code">
<pre> <span style="color: #008000;">//</span><span style="color: #008000;"> 2 发送粘性事件</span>
 EventBus.getDefault().postSticky(<span style="color: #0000ff;">new</span> StickyEvent(<span style="color: #800000;">"</span><span style="color: #800000;">我是粘性事件</span><span style="color: #800000;">"</span>));</pre>
</div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">　　//</span><span style="color: #008000;"> 3 接收粘性事件</span>
    @Subscribe(threadMode = ThreadMode.MAIN, sticky = <span style="color: #0000ff;">true</span><span style="color: #000000;">)
    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> StickyEventBus(StickyEvent <span style="color: #0000ff;">event</span><span style="color: #000000;">){
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 显示接收的数据</span>
        tv_eventbus_send_result.setText(<span style="color: #0000ff;">event</span><span style="color: #000000;">.msg);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #008000;">//</span><span style="color: #008000;"> 4.接收粘性事件数据按钮的点击事件处理</span>
 bt_eventbus_send_sticky.setOnClickListener(<span style="color: #0000ff;">new</span><span style="color: #000000;"> View.OnClickListener() {
  　　@Override
  　　</span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onClick(View v) {
   　　</span><span style="color: #008000;">//</span><span style="color: #008000;">true第一次点击，防止重复注册</span>
   　　　　<span style="color: #0000ff;">if</span><span style="color: #000000;">(isFirstFlag) {
    　　　　　　isFirstFlag </span>= <span style="color: #0000ff;">false</span><span style="color: #000000;">;
    　　　　　　</span><span style="color: #008000;">//</span><span style="color: #008000;"> 4 注册</span>
    　　　　　　EventBus.getDefault().register(EventBusSendActivity.<span style="color: #0000ff;">this</span><span style="color: #000000;">);
   　　　　}
  　　}
 });</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">@Override
    </span><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onDestroy() {
        super.onDestroy();
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> 5 解注册</span>
<span style="color: #000000;">        EventBus.getDefault().removeAllStickyEvents();
        EventBus.getDefault().unregister(EventBusSendActivity.</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
&nbsp;</div>
</div>