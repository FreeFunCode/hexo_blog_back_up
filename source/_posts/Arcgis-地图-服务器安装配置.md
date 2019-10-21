---
title: Arcgis-地图-服务器安装配置
date: 2014-11-08 04:00:13
tags: 地图
categories: 
- 后端
- CSharp
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/arcgis.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>1.安装IIS(IIS一键安装程序.exe)</p>
<p>2.安装虚拟光驱(DTLite4356-0091)</p>
<p>3.安装Arcgis10.0 desktop(具体查看HowTo.pdf)</p>
<p>1)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 先安装文件夹DotNet35/dotnetfx35sp1.exe</p>
<p>2）打开Arcgis10.0 desktop安装页面</p>
<p>a.先点击<img src="https://images0.cnblogs.com/blog/612293/201411/080339382063474.png" alt=""></p>
<p>b.</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080340258937239.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080341424408054.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080342193939697.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>3）安装Arcgis10.0 desktop安装页眉中的其中几项</p>
<p>a.如下所示</p>
<p>&nbsp;&nbsp;&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080342381127511.png" alt=""></p>
<p>&nbsp;</p>
<p>b.</p>
<p>&nbsp;</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080343064717348.png" alt=""></p>
<p>（注意，所有的都选择Entire feature will be installed on local hard drive）</p>
<p>c.</p>
<p><img src="https://images0.cnblogs.com/blog/612293/201411/080344155964197.png" alt=""></p>
<p>d. 找到ArcGIS10\ArcGIS10注册机</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080344544241768.png" alt=""></p>
<p>复制上面的内容之后替换掉 C:\Program Files\ArcGIS\License10.0\bin</p>
<p>里的service.txt 内容,修改 this_host 为你的计算机名</p>
<p>e.开始-程序-arcgis中打开ArcGIS Administrator</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080345221591261.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>4.安装Arcgis10.0 server(具体的安装跟desktop类似)</p>
<p>1）.<img src="https://images0.cnblogs.com/blog/612293/201411/080346047686144.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp; 注册：</p>
<p>&nbsp;</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080346389567532.png" alt=""></p>
<p>2）.</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080347439405813.png" alt=""></p>
<p>&nbsp;</p>
<p>3）.</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080347562061732.png" alt=""></p>
<p>&nbsp;</p>
<p>4）.</p>
<p>&nbsp;</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080348094874707.png" alt=""></p>
<p>&nbsp;</p>
<p>5）.</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080348198626171.png" alt=""></p>
<p>6）.地图发布</p>
<p>1&gt;.打开ArcCatalog 10</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080348520816362.png" alt=""></p>
<p>2&gt;.Add ArcGIS Server</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080349318622775.png" alt=""></p>
<p>双击</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080350070493692.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080350444403737.png" alt=""></p>
<p>&nbsp;</p>
<p><img src="https://images0.cnblogs.com/blog/612293/201411/080350576438969.png" alt=""></p>
<p>点击“Finish”后，</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080352023464335.png" alt=""></p>
<p>3&gt;.发布地图</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080354069097413.png" alt=""></p>
<p>加入地图所在目录</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080354295965483.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080355020963887.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080355152379904.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080355517994836.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080356024876270.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080356248158383.png" alt=""></p>
<p>4&gt;.查看是否发布成功</p>
<p>&nbsp;</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080356456439053.png" alt=""></p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080357083153894.png" alt=""></p>
<p>打开浏览器</p>
<p>&nbsp;<img src="https://images0.cnblogs.com/blog/612293/201411/080357252848508.png" alt=""></p>
<p>若页面如下图所示，则发布成功</p>
<p><img src="https://images0.cnblogs.com/blog/612293/201411/080357452532309.png" alt=""></p>
</div>