---
title: IIS网站应用偶尔出现服务不可用解决办法
date: 2014-09-15 13:40:05
tags: IIS
categories: 
- 后端
- CSharp
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/netframework.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>&nbsp;&nbsp;&nbsp; IIS网站应用偶尔出现"服务不可用"或者显示乱码字体，使用以下办法可以解决。</p>
<p>&nbsp;&nbsp;&nbsp; 原因：此种情况常会出现在iis是在Visual Studio或者.NET Framework之后安装发生，iis无法正确解析asp.net网站，但却<span style="color: #000000;">可以正常访问html页面</span>，</p>
<p>&nbsp;&nbsp;&nbsp; 1.依次打开“计算机管理→服务和应用程序→Internet信息服务→网站”，右键打开我们布置网站的属性，打开ASP.NET选项卡，查看是否选中 正确的.NET版本，本例的asp.net网站是建立在.NET 2.0环境下的，所以选择的ASP.NET版本为 2.0.50727，如果是.NET 4.0环境下的开发，请选择&nbsp;4.0.30319以此类推。</p>
<p>&nbsp;&nbsp;&nbsp;</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp; 2.打开“开始→运行→cmd”，在命令提示符中进行.NET环境的重新注册，</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; cd 定位到.NET的安装位置 <strong><span style="color: #ff0000;">C:\Windows\Microsoft.NET\Framework\v4.0.30319</span></strong>，</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 重新注册 <strong><span style="color: #ff0000;">aspnet_regiis.exe -i -enable</span></strong>。</p>
</div>