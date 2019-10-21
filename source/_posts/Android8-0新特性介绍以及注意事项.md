---
title: Android8.0新特性介绍以及注意事项
date: 2018-07-14 13:13:38
tags: Android
categories: 
- Android
- 知识点
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/android8_new_features.html)

<!-- more -->

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>&nbsp; &nbsp; &nbsp; &nbsp; 2017年8月22日，谷歌正式发布了Android 8.0的正式版，其正式名称为：<strong>Android Oreo</strong>（奥利奥） 。在此之前 临时代号叫: <strong>Android O</strong>。对应Api level 为<strong>26</strong>。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; 2017年12月5日 , 谷歌正式发布了Android 8.1的正式版。对应的Api Level 为<strong>27</strong> 。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130336131-671377976.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Powerful 强大&nbsp; &nbsp; &nbsp; &nbsp;Secure 安全&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Fast 流畅&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Smart&amp;seamiess&nbsp; 轻巧&amp;无缝</p>
<h1>Android 8.0 新特性:</h1>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 8.0版android系统主要聚焦点在电池续航能力,速度,安全.</p>
<h2>1.通知中心 -Notification Channel</h2>
<h3>&nbsp; &nbsp; &nbsp;1.1新特殊:</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 用户在通知界面可以发现顶部的快捷键有了更充裕的空间，并且根据网络大数据的用户使用频繁度调整了这几个快捷键的顺序；并且长按推送消息可以看到一个开关，操作它可以开启和关闭这个该应用的未来所有通知。而如果向左滑动通知，则会出现两个开关，一个是设置该应用的具体通知规范，另一个可以设置让该应用的通知推迟一段时间推送。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 长按操作:<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130403972-1193902772.png" alt="">&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;向左滑动: &nbsp;&nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130422381-1707747781.png" alt=""><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130432545-721904404.png" alt=""></p>
<h3>&nbsp; &nbsp; &nbsp;1.2注意事项:</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;NotificationChannel是android8.0新增的特性，如果App的targetSDKVersion&gt;=26，没有设置channel通知渠道的话，就会导致通知无法展示。报错内容:<strong>&nbsp; Failed to post notification on channel “null” Target Api is 26</strong></p>
<h3>&nbsp; &nbsp; &nbsp;1.3 解决方案</h3>
<h4>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1.3.1 临时方案</h4>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;临时兼容方案是设置targetSDKVersion低于26。</p>
<h4>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1.3.2 最终方案</h4>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;创建通知渠道</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Step1:创建 NotificationChannel 对象，并设置应用内唯一的通知 ID。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Step2:配置通知渠道的属性，比如提示声音/震动等。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Step3:在 NotificationManager 中注册通知渠道对象。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130456453-97306205.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;扩展 了解:</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp;<a href="https://stackoverflow.com/questions/45711925/failed-to-post-notification-on-channel-null-target-api-is-26">https://stackoverflow.com/questions/45711925/failed-to-post-notification-on-channel-null-target-api-is-26</a></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp;<a href="https://www.jianshu.com/p/92afa56aee05">https://www.jianshu.com/p/92afa56aee05</a></p>
<h2>2.PinnedShortcuts</h2>
<h3>&nbsp; &nbsp; &nbsp;2.1新特性</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; 安卓创造出了PinnedShortcuts功能，类似苹果的3DTouch，长按一个软件后可以弹出子菜单，然后就可以通过这个方式快捷的使用该应用的部分功能。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; 自定义ShortCuts 是7.1牛轧糖(API Level 25)版本新增的.</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; Shortcuts是指在桌面长按app图标而出现的快捷方式，可以为你的app的关键功能添加更加快速的入口而不是先打开app。类似苹果的3D Touch。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; 点击快捷方式可以访问应用功能，而且这种快捷方式也可以被拖拽到桌面的单独位置，变成单独的左面快捷方式。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130520370-1116257844.png" alt=""><img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130525067-1769221288.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 如果删除” 支付宝”应用,再点击” 付款码”,会提示”未安装应用”,随后,系统自动删除掉”付款码”快捷方式.</p>
<h3>&nbsp; &nbsp; &nbsp;2.2注意事项</h3>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;1.</strong>最多创建4个特定的shortcuts：目前虽然说Static shortcuts 和Dynamic shortcuts之和最多为5个，但实际上只会显示4个。当我们尝试添加第六个shortcut时, 应用会抛出异常: Java.lang.IllegalArgumentException: Max number of dynamic shortcuts exceeded.</p>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;2.</strong>限制 Label 长度：其中shortcutShortLabel建议不超过 10 个字符，shortcutLongLabel 建议不超过 25 个字符。</p>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;3.</strong>如何更好的删除(废弃)老的 Shortcut</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 这里主要考虑到删除老的 Shortcut，可能会影响已经固定的 Shortcut。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 对于静态 Shortcuts，直接删除配置文件中对应的 Shortcut 即可，系统桌面会将已固定的该 Shortcut 置灰，点击会提示 shortcutDisabledMessage。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 对于动态 Shortcuts 建议通过禁用的方式而不是直接删除的方式，因为已经删除的动态 Shortcut 如果被固定了依然是可用的，所以希望该入口不可用最好的方式是禁用。</p>
<h3>&nbsp; &nbsp; &nbsp;2.3 解决方案</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130651974-2003571495.png" alt=""></p>
<h4>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 2.3.1静态快捷方式</h4>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130707338-635356109.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;AndroidManifest.xml</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130733222-1243746562.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130741707-1997596608.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; res/xml/shortcuts</p>
<h4>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 2.3.2动态快捷方式</h4>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130803753-1058198975.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130811151-987638283.png" alt=""></p>
<p>&nbsp;</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130841345-123303880.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;ShortcutManager API可以帮助我们实现新建、更新、移除、禁用等快捷方式的操作.</p>
<p>&nbsp;</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 扩展了解:&nbsp; &nbsp;<a href="https://www.jianshu.com/p/c10ea2bd5803">https://www.jianshu.com/p/c10ea2bd5803</a></p>
<p>&nbsp;</p>
<h2>3.画中画</h2>
<h3>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 3.1新特性</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130900706-542659531.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130907952-164277144.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130916966-1253746177.png" alt=""></p>
<h3>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;3.2 使用方法&nbsp;</h3>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130936884-710201620.png" alt=""></p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;扩展了解:&nbsp;&nbsp;<a href="https://blog.csdn.net/ckwccc/article/details/79098602">https://blog.csdn.net/ckwccc/article/details/79098602</a></p>
<h2>4.后台限制</h2>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Android 8.0将进一步优化后台程序，减少应用在没完全退出后占用系统的资源，并且减少电量的消耗。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714130956396-737551763.png" alt=""></p>
<p>&nbsp;&nbsp;</p>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 注意事项</strong>:&nbsp;使用常驻service时候需要注意权限问题.</p>
<p>&nbsp;</p>
<h2>5. 自动保存密码</h2>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 这个功能将会保存用户的部分帐号和密码，用于在网站或者应用中的快速登录，效率提升了不少 。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714131014555-1049141484.png" alt=""></p>
<p>&nbsp;</p>
<h2>6. 设置菜单</h2>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 在新的Android 8.0系统中，设置的界面有了大幅变化，主菜单的覆盖性变得更广，更多的功能将在子菜单中体现，并且在菜单界面中重新设计了很多图标。</p>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 注意</strong>: 各大厂商都做了优化,呈现的方式多少都有点不一致.</p>
<h2>7. 字体优化</h2>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Android 8.0系统中谷歌还增加了对系统字体的更多支持，开发者可以自行更改字体样式，让用户有了更多字体的选择。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Android 8.0 和 Android 支持库 26 允许您从提供程序应用请求字体，而无需将字体绑定到 APK 中或让 APK 下载字体。此功能可减小 APK 大小，提高应用安装成功率，使多个应用可以共享同一种字体。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;扩展了解:&nbsp;<a href="https://blog.csdn.net/IO_Field/article/details/78016411">https://blog.csdn.net/IO_Field/article/details/78016411</a></p>
<h2>8. 表情符号</h2>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;Android 8.0还带来了符合Unicode 10标准的表情符号，比原来新加超过60个表情符，这也是比较明显的改变之一。</p>
<p>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;<img src="https://images2018.cnblogs.com/blog/612293/201807/612293-20180714131042984-1008393830.png" alt=""></p>
<p><strong>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 注意</strong>: 表情符号在与ios表情符同步时候,可能表现不出来.</p>
<p>&nbsp;</p>
</div>