---
title: Windows下搭建PHP开发环境，整合Apache+PHP+MySQL（举例软件为32位）
date: 2014-08-21 15:42:28
tags: php
categories: 
- 后端
- PHP
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/phpenvironment.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p><span style="color: #ff0000;"><strong><span style="font-size: 14pt;">原文来自：http://www.cnblogs.com/pharen/archive/2012/02/06/2340628.html</span></strong></span></p>
<p><span style="color: #ff0000;"><strong><span style="font-size: 14pt;">在原作者的基础上，只是做了详细的解释。避免走弯路。（注：红色字体为本人加的）</span></strong></span></p>
<p><strong><span style="font-size: 14pt;">一、准备工作-下载所需软件</span></strong></p>
<ul>
<li>Apache&nbsp; httpd-2.2.22-win32-x86-openssl-0.9.8t.msi</li>
<li>PHP&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; php-5.3.10-Win32-VC9-x86.zip</li>
<li>MySQL&nbsp;&nbsp; mysql-5.5.20-win32.msi</li>
</ul>
<p>&nbsp;</p>
<p>　　<strong><span style="font-size: 14pt;">二、安装软件</span></strong></p>
<p>　　　　<strong><span style="color: #008000;">安装Apache</span></strong>: 双击安装，与安装其他Windows软件没有什么区别，在填Server Infomation时，并没有特殊规定，只要输入的信息符合格式即可。</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620544829.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620553361.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620554265.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620555230.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620560244.png" alt=""></p>
<p>　　　　安装完成之后，在浏览器输入http://localhost，如果显示It Works!，表示Apache安装成功。</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020620583012.png" alt=""></p>
<p><span style="color: #ff0000;"><strong>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <span style="color: #ff0000;"><strong>Apache2.2</strong></span>安装目录：D:/Develop/Apache2.2</strong></span></p>
<p>&nbsp;</p>
<p>　　　　<strong><span style="color: #008000;">安装PHP</span></strong>:将php-5.3.10-Win32-VC9-x86.zip解压到一个目录即可。</p>
<p>&nbsp;&nbsp;&nbsp; <span style="color: #ff0000;">PHP安装目录：<strong>D:/Develop/PHP</strong></span></p>
<p><span style="color: #ff0000;">&nbsp;&nbsp;&nbsp; 应用程序部署目录：D:/Workspace/PHP （默认是：<strong>D:/Develop/Apache2.2/htdocs</strong></span><span style="color: #ff0000;">）</span></p>
<p>　　　　<span style="color: #008000;"><strong>安装MySQL</strong>:双击安装，如果需要更改安装目录，则在Choose Setup Type选择Custom</span></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020621052832.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020621062017.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020621064375.png" alt=""></p>
<p>　　　　安装完成后开始配置MySQL，全部保持默认选项即可，但最好把MySQL默认编码改为utf8，在Modify Security Settings选项中设置密码，输入两次密码即可完成，最后点Execute完成配置。</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020621105636.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020621111596.png" alt=""></p>
<p>&nbsp;</p>
<p>　　<span style="font-size: 14pt;"><strong>三、整合Apache+PHP+MySQL</strong></span></p>
<p>　　　　Apache : 首先修改Apache的配置文件，让Apache支持解析PHP文件。Apache配置文件在Apache安装目录的conf目录下的httpd.conf。</p>
<p>&nbsp;</p>
<p>　　　　1. 让Apache可以解析php文件，在配置文件中找到</p>
<p>　　　　　　　　#LoadModule vhost_alias_module modules/mod_vhost_alias.so</p>
<p>　　　　　 在下一行添加 (绿色的位置是根据PHP的所在目录而定的)</p>
<p>　　　　　　　　LoadModule php5_module "<strong><span style="color: #008000;">D:/Develop/PHP/php5apache2_2.dll</span></strong>"<br>　　　　　　　　PHPIniDir "<strong><span style="color: #008000;">D:/Develop/PHP</span></strong>"<br>　　　　　　　　AddType application/x-httpd-php .php .html .htm</p>
<p>&nbsp;</p>
<p>　　　　2. 在配置文件中找到</p>
<p>　　　　　　　　DirectoryIndex index.html</p>
<p>　　　　　 改为</p>
<p>　　　　　　　　DirectoryIndex <strong><span style="color: #008000;">index.php</span></strong> index.html</p>
<p>&nbsp;</p>
<p>　　　　3. 修改Apache站点目录，在配置文件中找到(Apache安装的目录不同，显示的值不一样)</p>
<p>　　　　　　　　DocumentRoot "<strong><span style="color: #008000;">D:/Develop/Apache2.2/htdocs</span></strong>"</p>
<p>　　　　　　改为</p>
<p>　　　　　　　　DocumentRoot "<strong><span style="color: #008000;">D:/Workspace/PHP</span></strong>"</p>
<p>　　　　　　</p>
<p>　　　　　　再找到</p>
<p>　　　　　　　　&lt;Directory "<strong><span style="color: #008000;">D:/Develop/Apache2.2/htdocs</span></strong>"&gt;</p>
<p>　　　　　　改为</p>
<p>　　　　　　　　&lt;Directory "<strong><span style="color: #008000;">D:/Workspace/PHP</span></strong>"&gt;　　</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<p>　　　　PHP : 把php.ini-development改名为php.ini，作为PHP的配置文件。修改php.ini</p>
<p>&nbsp;</p>
<p>　　　　　　1. 设置PHP扩展包的具体目录，找到</p>
<p>　　　　　　　　; On windows:<br>　　　　　　　　; extension_dir = "ext"</p>
<p>　　　　　　改为 (值是ext文件夹的目录)</p>
<p>　　　　　　　　; On windows:<br>　　　　　　　　&nbsp; extension_dir = "<strong><span style="color: #008000;">D:/Develop/PHP/ext</span></strong>"</p>
<p>&nbsp;</p>
<p>　　　　　　2. 开启相应的库功能，找到需要开启的库的所在行</p>
<p>　　　　　　　　;extension=php_curl.dll</p>
<p>　　　　　　　　;extension=php_gd2.dll</p>
<p>　　　　　　　　;extension=php_mbstring.dll</p>
<p>　　　　　　　　;extension=php_mysql.dll</p>
<p>　　　　　　　　;extension=php_xmlrpc.dll</p>
<p>　　　　　　 &nbsp; 去掉前面的分号(注释)，即改为</p>
<p>　　　　　　　　extension=php_curl.dll</p>
<p>　　　　　　　　extension=php_gd2.dll</p>
<p>　　　　　　　　extension=php_mbstring.dll</p>
<p>　　　　　　　　extension=php_mysql.dll</p>
<p>　　　　　　　　extension=php_xmlrpc.dll</p>
<p>　　　　　　</p>
<p>　　　　　　3. 设置时区，找到</p>
<p>　　　　　　　　;date.timezone =</p>
<p>　　　　　　　 改为</p>
<p>　　　　　　　　date.timezone = Asia/Shanghai</p>
<p>　　　　　　　</p>
<p>　　　　配置完成，检测一下配置是否成功。重启Apache，在站点目录下新建文件index.php，输入内容：</p>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre>&lt;?php<br>    <span style="color: #008080;">phpinfo();<br>?&gt;</span></pre>
</div>
<p>&nbsp;</p>
<p>　　打开浏览器输入http://localhost，显示如下内容，则安装成功，并且成功关联MySQL。</p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020622053727.png" alt=""></p>
<p><img style="display: block; margin-left: auto; margin-right: auto;" src="https://pic002.cnblogs.com/images/2012/330389/2012020622055921.png" alt=""></p>
</div>