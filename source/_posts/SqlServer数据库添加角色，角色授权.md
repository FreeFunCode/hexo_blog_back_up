---
title: SqlServer数据库添加角色，角色授权
date: 2014-12-02 17:15:01
tags: SqlServer
categories: 
- 后端
- CSharp
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/sqlgrant.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h4>&nbsp;&nbsp; 有时候我们需要跟第三方共享数据，但是又不能数据库完全开放。</h4>
<h4>&nbsp;&nbsp; 所以使用数据库角色授权就能很好的解决这个问题。</h4>
<h4>&nbsp;&nbsp; 以下是工作中用到的Sql语句。就当是做下笔记。</h4>
<p>&nbsp;</p>
<div>
<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_400258" class="syntaxhighlighter  sql"><div class="toolbar"><span><a href="#" class="toolbar_item command_help help">?</a></span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div><div class="line number12 index11 alt1">12</div><div class="line number13 index12 alt2">13</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="sql comments">--创建角色 role_TechDB</code></div><div class="line number2 index1 alt1"><code class="sql keyword">EXEC</code> <code class="sql plain">sp_addrole </code><code class="sql string">'role_TechDB'</code></div><div class="line number3 index2 alt2">&nbsp;</div><div class="line number4 index3 alt1"><code class="sql comments">--授予 role_TechDB 对 GXJG_JZGJCSJZLB,GXXX_YXSDWJBSJZL 表的所有权限</code></div><div class="line number5 index4 alt2"><code class="sql keyword">GRANT</code> <code class="sql keyword">SELECT</code><code class="sql plain">,</code><code class="sql keyword">DELETE</code><code class="sql plain">,</code><code class="sql keyword">INSERT</code><code class="sql plain">,</code><code class="sql keyword">UPDATE</code> <code class="sql keyword">ON</code> <code class="sql plain">GXJG_JZGJCSJZLB </code><code class="sql keyword">TO</code> <code class="sql plain">role_TechDB</code></div><div class="line number6 index5 alt1"><code class="sql keyword">GRANT</code> <code class="sql keyword">SELECT</code><code class="sql plain">,</code><code class="sql keyword">DELETE</code><code class="sql plain">,</code><code class="sql keyword">INSERT</code><code class="sql plain">,</code><code class="sql keyword">UPDATE</code> <code class="sql keyword">ON</code> <code class="sql plain">GXXX_YXSDWJBSJZL </code><code class="sql keyword">TO</code> <code class="sql plain">role_TechDB</code></div><div class="line number7 index6 alt2">&nbsp;</div><div class="line number8 index7 alt1"><code class="sql comments">--添加登录 zf_login,设置密码为zf@123456,默认数据库为TechDB</code></div><div class="line number9 index8 alt2"><code class="sql keyword">EXEC</code> <code class="sql plain">sp_addlogin </code><code class="sql string">'zf_login'</code><code class="sql plain">,</code><code class="sql string">'zf@123456'</code><code class="sql plain">,</code><code class="sql string">'TechDB'</code></div><div class="line number10 index9 alt1"><code class="sql keyword">EXEC</code> <code class="sql plain">sp_grantdbaccess </code><code class="sql string">'zf_login'</code><code class="sql plain">,</code><code class="sql string">'zf_login'</code></div><div class="line number11 index10 alt2">&nbsp;</div><div class="line number12 index11 alt1"><code class="sql comments">--添加 zf_login 为角色 role_TechDB 的成员</code></div><div class="line number13 index12 alt2"><code class="sql keyword">EXEC</code> <code class="sql plain">sp_addrolemember </code><code class="sql string">'role_TechDB'</code><code class="sql plain">,</code><code class="sql string">'zf_login'</code></div></div></td></tr></tbody></table></div></div>
</div>
</div>
</div>