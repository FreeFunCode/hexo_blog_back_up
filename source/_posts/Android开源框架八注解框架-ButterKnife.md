---
title: Android 开源框架 ( 八 ) 注解框架---ButterKnife
date: 2019-09-04 20:03:06
tags: 开源框架
categories: 开源框架
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/ButterKnife.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.引言</h2>
<div>&nbsp; &nbsp;ButterKnife是一个专注于Android系统的View注入框架</div>
<div>　了解注解框架,可以看这:<a id="post_title_link_9392534" href="https://www.cnblogs.com/bugzone/p/afinal.html">Android 开源框架 ( 四 ) Afinal --- Android 里的 ORM IOC聚合型框架</a><br>&nbsp;
<h2>&nbsp;二.使用</h2>
<h3>&nbsp;1. AndroidStudio 添加ButterKnife插件使用。</h3>

&nbsp;　　<strong>Settings ---&gt; Plugins ----&gt;搜索ButterKnife</strong><br>&nbsp;看图：<br>&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201808/612293-20180801074431484-1364962086.png" alt=""><br>&nbsp;<br>&nbsp;在build.gradle中引用ButterKnife库:</div>
<div>
<div class="cnblogs_code">
<pre>compile <span style="color: #800000;">'</span><span style="color: #800000;">com.jakewharton:butterknife-compiler:8.5.1</span><span style="color: #800000;">'</span></pre>
</div>
<h3>&nbsp;2.利用ButterKnife一键实现批量findViewById()</h3>
&nbsp;在对应的Activity页右键 ---&gt; Generate(Alt+Insert) ---&gt; ButterKnife 使用插件</div>
<div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color: #000000;">@BindView(R.id.tv_first)
    TextView tvFirst;
    @BindView(R.id.tv_info)
    TextView tvInfo; 
 
 @Override
    </span><span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(</span><span style="color: #0000ff;">this</span><span style="color: #000000;">);
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>&nbsp;</p>
<br>&nbsp;<br>&nbsp;</div>
</div>