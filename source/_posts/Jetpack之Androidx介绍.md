---
title: Jetpack之Androidx介绍
date: 2019-11-01 20:41:33
tags: Androidx
categories: 
- Jetpack
- Androidx
---

# Jetpack之Androidx介绍

#### 一. 由来
在Google I/O 2018上，Android团队宣布了AndroidX。

 AndroidX 是对 android.support.* 包的整理后产物。由于之前的 support 包过于混乱，所以，Google 推出了AndroidX。

因此，AndroidX本质上其实就是对Android Support Library进行的一次升级。

自support v7:28开始，大部分support包都会迁移到androidx下。

##### android.support.*包介绍：
如果在低版本Android平台上开发一个应用程序，而应用程序又想使用高版本才拥有的功能，就需要使用Support库。

- Android Support v4: 这个包是为了照顾1.6及更高版本而设计的，这个包是使用最广泛的，eclipse新建工程时，都默认带有了。

- Android Support v7: 这个包是为了考虑照顾2.1及以上版本而设计的，但不包含更低，故如果不考虑1.6,我们可以采用再加上这个包，另外注意，v7是要依赖v4这个包的，即：两个得同时被包含。

- Android Support v13 : 这个包的设计是为了android 3.2及更高版本的，一般我们都不常用，平板开发中能用到。

<!-- more -->

![AndroidSupportLibrary](/images/androidsupport.jpg)

#### 二.基本概念
AndroidX 是 Android 团队用于在 Jetpack 中开发、测试、打包和发布库以及对其进行版本控制的开源项目。

AndroidX 对原始 Android 支持库进行了重大改进。与支持库一样，AndroidX 与 Android 操作系统分开提供，并与各个 Android 版本向后兼容。AndroidX 完全取代了支持库，不仅提供同等的功能，而且提供了新的库。此外，AndroidX 还包括以下功能：

- AndroidX 中的所有软件包都使用一致的命名空间，以字符串 androidx 开头。支持库软件包已映射到对应的 androidx.* 软件包。有关所有旧类到新类以及旧编译工件到新编译工件的完整映射，请参阅软件包重构页面。

- 与支持库不同，AndroidX 软件包会单独维护和更新。androidx 软件包使用严格的语义版本控制，从版本 1.0.0 开始。您可以单独更新项目中的 AndroidX 库。

- 所有新支持库的开发工作都将在 AndroidX 库中进行。这包括维护原始支持库工件和引入新的 Jetpack 组件。

------

#### 三.使用 AndroidX
如果要在新项目中使用 AndroidX，则需要将编译 SDK 设置为 Android 9.0（API 级别 28）或更高版本，并在 gradle.properties 文件中将以下两个 Android Gradle 插件标记设置为 true。

`android.useAndroidX`：如果设置为 true，Android 插件会使用相应的 AndroidX 库，而非支持库。如果未指定，则该标记默认为 false。
`android.enableJetifier`：如果设置为 true，Android 插件会重写其二进制文件，自动迁移现有的第三方库以使用 AndroidX。如果未指定，则该标记默认为 false。

##### 迁移到AndroidX

AndroidX 会将原始支持库 API 软件包映射到 androidx 命名空间。只有软件包和 Maven 工件名称发生了变化；类、方法和字段名称没有改变。

###### 使用 Android Studio 迁移现有项目
借助 Android Studio 3.2 及更高版本，您可以通过从菜单栏中依次选择` Refactor > Migrate to AndroidX`，快速迁移现有项目以使用 AndroidX。

如果您有任何尚未迁移至 AndroidX 命名空间的 Maven 依赖项，那么当您在` gradle.properties` 文件中将以下两个标记设置为 true 时，Android Studio 编译系统也会为您迁移这些依赖项：

`android.useAndroidX=true`
`android.enableJetifier=true`
    

要迁移未使用任何第三方库但带有需要转换的依赖项的现有项目，可以将 android.useAndroidX 标记设置为 true，并将 android.enableJetifier 标记设置为 false。

