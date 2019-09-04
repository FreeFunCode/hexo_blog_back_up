---
title: VS发布报错未能将文件……复制到……
date: 2015-06-01 21:25:08
tags: visualStudio
categories: CSharp后端
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/notfund.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>　　　　最近在一个老项目的基础上改造和升级准备做一个“产品”出来。等我利用业余时间弄得差不多就可以公布出来了。希望时间不要太长。</p>
<p>　　-----------------------------------------------------------------------------------------------------------------------------------</p>
<p>　　入主题：</p>
<p>　　删除了一些图片文件,发布的时候发现报错了，编译并不会报错。</p>
<p>　　错误大致是：<strong>未能将文件../Images/...jpg 复制到 obj\Debug\Package\PackageTmp\.. 中</strong>，当然不限于图片文件。</p>
<p>　　因为花了一些时间，所以记录下。</p>
<p>　　产生的原因：之所以发布时出现这种错误，是因为之前在<span lang="EN-US">.csproj项目文件中“<span lang="EN-US">&lt;<span lang="EN-US">Content Include&gt;”参数已经存储了这些文件路径。后来有些文件被删掉了，但是.csproj中参数记录还在，所以发布时才会出现“未能将文件……复制到……”的错误。</span></span></span></p>
<p>　　网上找来的解决办法：</p>
<p align="left"><strong>　　方法1</strong>、比较笨的方法就是按照出错提示暂时把所缺的图片重新放入相应文件夹里，这样再次发布成功，但这样一是麻烦二是这些图片对目前的网站来说根本就是垃圾图片，没有任何用处，浪费硬盘空间，增加服务器压力。</p>
<p align="left"><strong>　　方法2</strong>、所以网上有人给出解决方案：删掉.csproj文件中相应的&lt;Content Include&gt;行，经测试可行，但是这样还是麻烦的不行，如果是大量图片文件分多目录存放（如每一天生成一个文件夹），徒手删除操作何其繁琐。</p>
<p align="left"><strong>　　方法3</strong>、基于之前的方法繁琐，我想到了一个其实也很笨的方法，先把upfile整个目录在解决方案资源管理器中“从项目中排除”，然后“重新生成解决方案”，然后再发布，最后把upfile目录拷贝到发布后的网站目录中，测试可行。</p>
<p align="left">　　综上：我的方法的差不多，<strong>如果文件少，那你可以用方法2。如果文件比较多，并且你已经不知道是那些文件里，那就把.csproj文件中所有的&lt;Content Include&gt;删除，删除后在vs上就显示被排除在项目之外了，在把你自己的项目文件包含下。就可以了。</strong></p>
<p align="left"><strong>　　</strong></p>
</div>