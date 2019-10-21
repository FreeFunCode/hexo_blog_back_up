---
title: bsdiff&bspatch增量更新概念介绍
date: 2019-10-07 10:22:16
tags: 增量更新
categories: 
- Android
- 知识点
---

> 随着Android生态，技术越来越成熟，目前市场很多Android的项目工程很大，团队人数多，慢慢衍生出了许多组件化插件化技术。
> 同时也因为Android安装包apk也在逐渐增大，每次发布，用户更新apk环境复杂，如果全量更新下载apk的话在使用流量情况下，网络环境不好等等，对于用户体验是非常不好的。

#### 首先区分下几个概念

**组件化** ： 把常用的模块代码,抽取lib工程或者jar达到复用的效果。
**插件化**：目的是把需要实现的模块或功能当做一个独立的提取出来，减少宿主的规模，当需要使用到相应的功能时再去加载相应的模块。涉及动态代理，ClassLoader，以及另一个apk资源的加载。例如：360的DroidPlugin (推荐)
**热修复**：往往是从修复bug的角度出发，强调的是在不需要二次安装应用的前提下修复已知的bug(涉及关键词：Hook技术、动态代理等)。例如：阿里 AndFix。
**增量更新**：APK增量更新是很多大厂APP采用的技术。bsdiff库生成补丁文件方式下载跟旧版本APK合成生成新版APK的原理(ligbspatch.so)。手机游戏app增量更新使用较多。例如：[SmartAppUpdates](https://github.com/cundong/SmartAppUpdates)

<!-- more -->

-----

![bsdiff&bspatch](/images/bsdiff&bspatch.jpg)

**bsdiff生成patch -> bzip2压缩 -> android下载patch -> bzip2解压patch -> bspatch合并patch -> 新的apk**
> bsdiff并不是专门为apk增量更新设计的，它可以对任何二进制文件进行差分和合并。bzip2的功能是利用哈夫曼编码对文件进行无损压缩(将差分包进行压缩便于网络传输)和解压。

流程：
- 在服务器端，生成两个版本apk的差分包；
- 在手机客户端，使用已安装的apk与这个差分包进行合成，得到新版的apk；
- 校验新合成的apk文件是否完整，MD5或SHA1是否正确，如正确，则引导用户安装；

-----

#### 常用命令 

bsdiff 生成patch包        命令：`bsdiff oldfile newfile patchfile`           例如: `bsdiff xx_v1.0.apk xx_v2.0.apk xx.patch`

bspatch生成新的APK：    命令： `bspatch oldfile newfile patchfile`      例如: `bsdiff xx_v1.0.apk xx_v2.0.apk xx.patch`


#### 使用

[Android增量更新——bsdiff&bspatch](https://blog.csdn.net/u012484172/article/details/54932717)


#### 缺点：
增量升级并非完美无缺的升级方式，至少存在以下两点不足：

- 增量升级是以两个应用版本之间的差异来生成补丁的，你无法保证用户每次的及时升级到最新，所以你必须对你所发布的每一个版本都和最新的版本作差分，以便使所有版本的用户都可以差分升级，这样操作相对于原来的整包升级较为繁琐，不过可以通过自动化的脚本批量生成。

- 增量升级成功的前提是，用户手机端必须有能够让你拷贝出来且与你服务器用于差分的版本一致的apk，这样就存在，例如，系统内置的apk无法获取到，无法进行增量升级；对于某些与你差分版本一致，但是内容有过修改的(比如破解版apk)，这样也是无法进行增量升级的，为了防止合成补丁错误，最好通过md5 或者其他方式对patch包进行完整性的校验。