---
title: 错误：类型System.Object未定义或者不能引入项目
date: 2015-06-07 14:33:14
tags: c#
categories: 
- 后端
- CSharp
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/mscorlib.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>最近在研究一些网上开源的工作流，下了源代码，发布遇到了问题。“类型 'System.Object' 为定义或者不能引入”，想引入.dll,还不让引入进来。</p>
<p>&nbsp;</p>
<p>网上搜了下，大部分是将visual studio关下，在打开，就没事了。但是我电脑都重启，还是没解决。</p>
<p>&nbsp;</p>
<p>最后，使用下面这个方法解决的，这个方法搜起来比较麻烦，还不好找，所以记录下，方便自己，方便别人。http://i.cnblogs.com/EditPosts.aspx?opt=1</p>
<p>&nbsp;</p>
<p><strong>问题：</strong></p>
<p>项目之前引用了mscorlib 或 System.Core，但是vs2013上显示黄色警告小图标，说明文件丢失了，手动删除了，准备重新引入，但是引不进来。</p>
<p>&nbsp;</p>
<p><strong>办法如下：</strong></p>
<p>想要引用 mscorlib 或 System.Core 被删了，提示类型 'System.Object'，你手工加上这个引用加不进去。</p>
<p>让你用记事本打开对应项目的 .csproj 文件。在里面加你要引入的dll名称：</p>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_841013" class="syntaxhighlighter  csharp"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="csharp plain">&lt;Reference Include=</code><code class="csharp string">"mscorlib"</code> <code class="csharp plain">/&gt;</code></div><div class="line number2 index1 alt1"><code class="csharp plain">&lt;Reference Include=</code><code class="csharp string">"System.Core"</code> <code class="csharp plain">/&gt;</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog2015/612293/201506/071431161292531.png" alt=""></p>
</div>