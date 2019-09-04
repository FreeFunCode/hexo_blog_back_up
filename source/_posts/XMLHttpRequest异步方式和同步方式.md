---
title: XMLHttpRequest异步方式和同步方式
date: 2014-09-28 20:19:04
tags: XMLHttpRequest
categories: CSharp后端
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/XMLHttpRequest.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p><strong>一.同步和异步区别</strong></p>
<p><span style="color: #000000;">　　简单说：同步是阻塞模式，异步是非阻塞模式。</span></p>
<p><span style="color: #000000;">　　举个例子：普通B/S模式（同步）AJAX技术（异步）<br>　　　　同步：提交请求-&gt;等待服务器处理-&gt;处理完毕返回 这个期间客户端浏览器不能干任何事<br>　　　　异步: 请求通过事件触发-&gt;服务器处理（这是浏览器仍然可以作其他事情）-&gt;处理完毕</span></p>
<p><strong>二.XMLHttpRequest 异步方式和同步方式</strong></p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_860691" class="syntaxhighlighter  javascript"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="javascript plain">xmlHttp.open(</code><code class="javascript string">"GET"</code><code class="javascript plain">, url, </code><code class="javascript keyword">false</code><code class="javascript plain">);</code><code class="javascript comments">//同步方式请求&nbsp; </code></div><div class="line number2 index1 alt1"><code class="javascript plain">xmlHttp.open(</code><code class="javascript string">"GET"</code><code class="javascript plain">, url, </code><code class="javascript keyword">true</code><code class="javascript plain">)</code><code class="javascript comments">//异步 </code></div></div></td></tr></tbody></table></div></div>
</div>
<p><strong>&nbsp;三.项目举例</strong></p>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 在项目中有时候需要用到XMLHttpRequest，但是什么时候用同步，什么时候用异步，这个有时候差距看不出来，但是有时候错误出现了，还不知道什么原因，有时候正确，有时候执行有错误了。</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 以使用XMLHttpRequest来进行上传文件做测试，我们需要先上传文件到服务器，然后保存表单信息并保存上传文件的url。</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 以下是正确代码，使用了同步方式。</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_913084" class="syntaxhighlighter  javascript"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="javascript keyword">var</code> <code class="javascript plain">fd = </code><code class="javascript keyword">new</code> <code class="javascript plain">FormData();</code></div><div class="line number2 index1 alt1"><code class="javascript plain">fd.append(</code><code class="javascript string">"fileToUpload"</code><code class="javascript plain">, top.window.document.getElementById(</code><code class="javascript string">'fileToUpload'</code><code class="javascript plain">).files[0]);</code></div><div class="line number3 index2 alt2"><code class="javascript keyword">var</code> <code class="javascript plain">xhr = </code><code class="javascript keyword">new</code> <code class="javascript plain">XMLHttpRequest();</code></div><div class="line number4 index3 alt1"><code class="javascript plain">xhr.open(</code><code class="javascript string">"POST"</code><code class="javascript plain">, WEB + </code><code class="javascript string">"/Thesis/ImportPdf"</code><code class="javascript plain">,</code><code class="javascript keyword">false</code><code class="javascript plain">);</code><code class="javascript comments">//上传文件到服务器</code></div><div class="line number5 index4 alt2"><code class="javascript plain">xhr.send(fd);</code></div><div class="line number6 index5 alt1"><code class="javascript plain">xhr.addEventListener(</code><code class="javascript string">"load"</code><code class="javascript plain">, uploadComplete(dialog), </code><code class="javascript keyword">false</code><code class="javascript plain">); </code><code class="javascript comments">//上传完毕后，保存表单信息以及上传文件路径信息&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </code></div></div></td></tr></tbody></table></div></div>
</div>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 上面代码有两处需要往下执行，分别是：xhr.open("POST", WEB + "/Thesis/ImportPdf",false);和xhr.addEventListener("load", uploadComplete(dialog), false);</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 如果我们使用xhr.open("POST", WEB + "/Thesis/ImportPdf",true);//异步方式，如果文件小，可能照常本来第二步需要保存路径信息也能取到，但是文件太大，上传需要时间。异步的话，第一步上传文件还没上传完毕，第二步就执行了，但最终我们获取不到上传文件路径信息。所以需要使用阻塞式的同步方式。</p>
<p>&nbsp;</p>
</div>