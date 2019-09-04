---
title: 在GitHub上搭建hexo博客
date: 2014-11-23 14:06:58
tags: hexo
categories: Hexo
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/hexo.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>在GitHub上我们可以做许多事，我还在不断探索中...</p>
<p>在GitHub上搭建博客，可以有多种方法，但是我遇到的，最好的应该是hexo了。</p>
<p>在GitHub上搭建hexo博客流程如下：</p>
<h1>一.安装Git</h1>
<p>地址：http://msysgit.github.io/</p>
<h1>二.安装Nodejs</h1>
<p>地址：http://nodejs.org/</p>
<p>如果安装时候提示，无用的软件包，那换个低版本的，可能与你系统不兼容造成的。</p>
<h1>三.安装hexo</h1>
<h2>1.使用npm命令安装hexo&nbsp;</h2>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_347255" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">npm install -g hexo</code></div></div></td></tr></tbody></table></div></div>
</div>
<h2><img src="https://images0.cnblogs.com/blog/612293/201411/231413480787180.png" alt="">&nbsp;</h2>
<h2>2.创建一个存放hexo的文件夹（例如：d:myhexo）</h2>
<p>git命令窗口 cd&nbsp;到你创建的文件夹下，使用hexo命令初始化下</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_356076" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">hexo init</code></div></div></td></tr></tbody></table></div></div>
</div>
<h2>&nbsp;3.安装依赖包</h2>
<p>使用npm命令来安装依赖包</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_650126" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">npm install</code></div></div></td></tr></tbody></table></div></div>
</div>
<h2><img src="https://images0.cnblogs.com/blog/612293/201411/231414433753154.png" alt="">&nbsp;</h2>
<h2>4.启动服务</h2>
<p>hexo命令生产;</p>
<p>启动服务，使用浏览器，地址http://localhost:4000查看</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_176595" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">hexo generate</code></div><div class="line number2 index1 alt1"><code class="html plain">hexo server</code></div></div></td></tr></tbody></table></div></div>
</div>
<h1>四.部署到GitHub</h1>
<h2>1.首先创建一个repo(项目名：你的github账户名.github.io)</h2>
<h2>2.编辑 <code>_config.yml 配置文件（在根目录下面）。</code></h2>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_764963" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">deploy:</code></div><div class="line number2 index1 alt1"><code class="html spaces">&nbsp;&nbsp;</code><code class="html plain">type: github</code></div><div class="line number3 index2 alt2"><code class="html spaces">&nbsp;&nbsp;</code><code class="html plain">repository: https://github.com/你的github账户名/你的github账户名.github.io.git</code></div><div class="line number4 index3 alt1"><code class="html spaces">&nbsp;&nbsp;</code><code class="html plain">branch: master</code></div></div></td></tr></tbody></table></div></div>
</div>
<h2>3.生成，发布</h2>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_381977" class="syntaxhighlighter  html"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="html plain">hexo generate</code></div><div class="line number2 index1 alt1"><code class="html plain">hexo deploy</code></div></div></td></tr></tbody></table></div></div>
</div>
<h2>&nbsp;4.查看http://<code>你的github账户名.github.io 是否成功（一般需要10分钟才有效）</code></h2>
<p><img src="https://images0.cnblogs.com/blog/612293/201411/231415220469537.png" alt=""></p>
</div>