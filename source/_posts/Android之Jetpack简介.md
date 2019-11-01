---
title: Android_Jetpack简介
date: 2019-11-01 20:39:40
tags: Jetpack
categories: 
- Jetpack
---

# Android之Jetpack简介

>  2017 年的 Google IO 宣布 Kotlin 成为 Android 开发的官方语言。
>  2018年谷歌I/O 发布了一系列辅助android开发者的实用工具，合称Jetpack，以帮助开发者构建出色的 Android 应用。

#### 一.什么是Jetpack：
 Jetpack 是一套库、工具和指南，可帮助开发者更轻松地编写优质应用。这些组件可帮助您遵循最佳做法、让您摆脱编写样板代码的工作并简化复杂任务，以便您将精力集中放在所需的代码上。
换言之，Google利用Jetpack将一些优秀的Android组件库进行了标准化。


#### 二.作用：
Android Jetpack 完美兼容 Kotlin 语言，利用 Android KTX 可大幅节省代码量。
Jetpack 包含与平台 API 解除捆绑的 androidx.* 软件包库。这意味着，它可以提供向后兼容性，且比 Android 平台的更新频率更高，以此确保您始终可以获取最新且最好的 Jetpack 组件版本。
- 加速开发：组件可以单独采用（不过这些组件是为协同工作而构建的），同时利用 Kotlin 语言功能帮助您提高工作效率。
- 消除样板代码：Android Jetpack 可管理繁琐的 Activity（如后台任务、导航和生命周期管理）。以便您可以专注于如何让自己的应用出类拔萃。
- 构建高质量的强大应用：Jetpack 组件围绕现代化设计实践构建而成，具有向后兼容性，可以减少崩溃和内存泄漏。

<!-- more -->

#### 三.Jetpack包含那些组件：
 Android Jetpack 组件覆盖以下 4 个方面：基础（Foundation）、架构（Architecture）、行为（Behavior） 、界面（UI）。

![jetpact1](/images/jetpact1.png)

##### Foundation
包含：Android KTX，AppCompat, Auto, 检测, Multidex, 安全, 测试, TV，Wear OS by Google。

###### Android KTX
Android KTX 是一组 Kotlin 扩展程序，属于 Android Jetpack 系列。它优化了供 Kotlin 使用的 Jetpack 和 Android 平台 API。Android KTX 旨在让您利用 Kotlin 语言功能（例如扩展函数/属性、lambda、命名参数和参数默认值），以更简洁、更愉悦、更惯用的方式使用 Kotlin 进行 Android 开发。Android KTX 不会向现有的 Android API 添加任何新功能。

###### AppCompat
在较低版本的Android 系统上恰当地降级。AppCompat就是指v7 appcompat库。
此库添加了对操作栏用户界面设计模式的支持。这个库包括对Material Design用户界面实现的支持。也就是说，我们可以借助该库，对Material Design有更便捷和兼容性更好的实现。

###### Auto
Android Auto 提供了适用于所有车辆的标准化界面和用户交互模式。作为设计者，您无需担心车辆特有的硬件差异（如屏幕分辨率、软件界面、旋钮和触摸式控件）。

###### 检测
快速对基于 Kotlin 或 Java 的代码进行基准化分析。该库会处理预热，衡量代码性能，并将基准化分析结果输出到 Android Studio 控制台。由于这些步骤涉及停用调试功能以获得准确的性能结果，因此您不会将更改提交至源代码控制系统中。

###### Multidex
方法数超过 64K 的应用启用多 dex 文件（ 65,536 方法数限制）。

##### Architecture
包含：DataBind, Lifecycles, LiveData, Navigation, Paging, Room, ViewModel, WorkManager.

###### DataBind
可以使用声明性格式（而非程序化地）将布局中的界面组件绑定到应用中的数据源。
即：将布局组件与源数据绑定，使源数据变化的同时布局组件及时同步更新。

###### Lifecycles
用来管理和响应 Activity / Fragment 的生命周期的变化，帮助我们编写出更易于组织且通常更加轻量级的代码，让代码变得更易于维护。
Lifecycle 是一个类，它持有 Activity / Fragment 生命周期状态的信息，并允许其它对象观察此状态。

###### LiveData
LiveData是一个可观察的数据持有者类。与常规observable不同，LiveData是生命周期感知的。

###### Navigation
是指支持用户导航、进入和退出应用中不同内容片段的交互。Android Jetpack 的导航组件可帮助您实现导航，无论是简单的按钮点击，还是应用栏和抽屉式导航栏等更为复杂的模式，该组件均可应对。导航组件还通过遵循一套既定原则来确保一致且可预测的用户体验。

###### Paging
逐步从您的数据源按需加载信息。
分页库。

###### Room
Room是Google为了简化旧式的SqlLite操作专门提供的一个SqlLite的ORM抽象层框架库。

###### ViewModel
 是以生命周期的方式存储与管理UI相关数据。

###### WorkManager
管理一些要在后台工作的任务, -- 即使你的应用没启动也能保证任务能被执行。
例如：
向后端服务发送日志分析
定期与服务器同步应用程序数据

![jetpact2](/images/jetpact2.png)