---
title: 使用fiddler抓手机端http数据包
date: 2018-05-06 19:48:10
tags: fiddler
categories: Android
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/fiddler.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <h2>一.fiddler工具用途</h2>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 很广泛。大部分的用法是拦截HTTP请求，分析request和response的具体细节，协助排查前后端联调开发中的问题。当然还可以打HTTP“断点”，修改request请求，绕过前端js限制。这个不限于本地还是remote服务器。因此，对于高手来说，前端js的限制基本不算限制。黑客工具中类似的是burpsuite，功能还要强大。</p>
<p>&nbsp;</p>
<h2>二.fiddler工具介绍</h2>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 1.原理是以web代理服务器的形式进行工作的。代理就是在客户端和服务器之间设置一道关卡，客户端先将请求数据发送出去后，代理服务器会将数据包进行拦截，代理服务器再冒充客户端发送数据到服务器；同理，服务器将响应数据返回，代理服务器也会将数据拦截，再返回给客户端。</p>
<p>　　2.Fiddler可以抓取支持http代理的任意程序的数据包，如果要抓取https会话，要先安装证书。</p>
<h2>三.fiddler工具抓app包使用方法</h2>
<p>　　1.下载安装fiddler后。安装https协议证书。Tools =&gt;Options =&gt;HTTPS =&gt;&nbsp;勾选 Decrypt HTTPS traffic =&gt;勾选 Ignore server certificate errors(unsafe) 默认会安装协议证书，同意安装即可。&nbsp;</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201805/612293-20180506193544627-501906284.png" alt=""></p>
<p>　　2.配置可远程连接 tools =&gt;Options =&gt;Connections =&gt;勾选 Allow remote computers to connect.</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201805/612293-20180506193955799-1498479438.png" alt=""></p>
<p>　　3.查看当前安装fiddler电脑的局域网ip</p>
<p><img style="float: left;" src="https://images2018.cnblogs.com/blog/612293/201805/612293-20180506194221875-848894338.png" alt=""></p>
<p>&nbsp;　　</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>3.手机与电脑在同一个局域网内。配置手机wifi的代理服务器（即安装了fiddler的电脑）ip地址和端口号。ip地址在第3步；端口号在第2部，默认是8888.</p>
<p><img src="https://images2018.cnblogs.com/blog/612293/201805/612293-20180506194424881-544225805.jpg" alt="" width="367" height="652"></p>
<p>&nbsp;</p>
<p>总结：</p>
<p>通过Fiddler可以抓取请求和响应参数，通过对参数进行分析，可以定位是前端还是后台问题。在尤其是分析无法本地IDE调试的线上生产环境下的app版本时候很有用。</p>
<p>&nbsp;</p>
</div>