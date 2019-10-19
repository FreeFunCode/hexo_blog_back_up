---
title: Android-kotlin-coroutine协程的概念
date: 2019-10-19 16:40:15
tags: coroutine协程
categories: 
- Android
- kotlin
---

# Android-kotlin-coroutine协程的概念

#### 一. 进程，线程，协程 概念
##### 进程与线程
- 进程是资源分配的最小单位，线程是程序执行的最小单位。

- 进程有自己的独立地址空间，每启动一个进程，系统就会为它分配地址空间，建立数据表来维护代码段、堆栈段和数据段，这种操作非常昂贵。而线程是共享进程中的数据的，使用相同的地址空间，因此CPU切换一个线程的花费远比进程要小很多，同时创建一个线程的开销也比进程要小很多。
> 为了加大一个应用可使用的内存通过多进程来获取多份内存空间
> 通过给四大组件指定android:process属性可以轻易开启多进程

- 线程之间的通信更方便，同一进程下的线程共享全局变量、静态变量等数据，而进程之间的通信需要以通信的方式（IPC-跨进程通信)进行。不过如何处理好同步与互斥是编写多线程程序的难点。
> 跨进程通信方式有：
> 
> 1. 通过Intent(Bundle)附加extras来传递信息
>
> 2. 通过共享文件来共享数据
>
> 3. 采用Binder方式来是想跨进程通信
>
> 4. 采用ContentProvider
>
> 5. 采用socket

- 但是多进程程序更健壮，多线程程序只要有一个线程死掉，整个进程也死掉了，而一个进程死掉并不会对另外一个进程造成影响，因为进程有自己独立的地址空间。
> 延申到android崩溃同样道理。
> 
> app中大量Web页面的使用容易导致App内存占用巨大，存在内存泄露，崩溃率高等问题，WebView独立进程的使用是解决Android WebView相关问题的一个合理的方案。

![进程与线程](/images/进程与线程.jpg)

##### 线程与协程
> kotlin协程是一种用户态的轻量级线程。一个进程可以拥有多个线程一样，一个线程也可以拥有多个协程。
> 
> 协程不是被操作系统内核所管理，而完全是由程序所控制（也就是在用户态执行）。
> 
> 协程的开销远远小于线程的开销。

协程的特点在于是单线程执行，那和多线程比，协程有何优势？换句话说，协程的出现解决了线程的那些痛点。
- **极高的执行效率**：因为子程序切换不是线程切换，而是由程序自身控制，因此，没有线程切换的开销，和多线程比，线程数量越多，协程的性能优势就越明显；
- **不需要多线程的锁机制**：因为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁，只需要判断状态就好了，所以执行效率比多线程高很多。

![线程与协程](/images/线程与协程.jpg)

##### 小结：
进程：拥有自己独立的堆和栈，既不共享堆，也不共享栈，进程由操作系统调度；
线程：拥有自己独立的栈和共享的堆，共享堆，不共享栈，标准线程由操作系统调度；
协程：拥有自己独立的栈和共享的堆，共享堆，不共享栈，协程由程序员在协程的代码里显示调度。

#### 二. Kotlin协程基本使用
协程主要是让原来要使用"异步+回调方式"写出来复杂代码，简化成可以用看似同步的方式，这样我们就可以按串行的思维模式去组织原本分散在不同上下文的代码逻辑。也增强了程序的可读性。
```
//----例如：伪代码----
launch(Background) {
  //执行耗时操作
  val bitmap = MediaStore.getBitmap(uri) 
  launch(UI) {
    //更新UI
    imageView.setImageBitmap(bitmap)
  }
}
```

##### 集成环境
- 集成kotlin插件
```
ext.kotlin_version = '1.3.11'
dependencies {
    classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
}
```
- 引入协程核心库
```
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:1.1.0"
//或使用android
implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.1.0"
```
- experimental启用声明
```
//在module的build.gradle中声明
kotlin {
    experimental {
        coroutines 'enable'
    }
}
```
##### 创建
- **launch**
 创建协程
- **async**
创建带返回值的协程，返回的是 Deferred 类
- **withContext**
不创建新的协程，在指定协程上运行代码块
- **runBlocking**
不是 GlobalScope 的 API，可以独立使用，区别是 runBlocking 里面的 delay 会阻塞线程，而 launch 创建的不会。
```
    //launch
    launch{
          delay(1000)
          println("launch")
     }

    //async 有返回值
    async {
        delay(1000)
        println("async")
        //async 才能有return
        return@async ""
    }
```
##### 协程上下文-CoroutineContext
- **Dispatchers.Default**
共享后台线程池里的线程

- **Dispatchers.Main**
Android主线程

- **Dispatchers.IO**
共享后台线程池里的线程

- **Dispatchers.Unconfined**
不限制，使用父Coroutine的现场

- **newSingleThreadContext**
使用新的线程
```
 /**
  * 上下文
  */
 launch { // 运行在父协程的上下文中，即 runBlocking 主协程
     println("main runBlocking      : I'm working in thread ${Thread.currentThread().name}")
 }
 launch(Dispatchers.Default) { // 将会获取默认调度器
     println("Default               : I'm working in thread ${Thread.currentThread().name}")
 }
 launch(Dispatchers.Main) { // Android主线程
     println("Main               : I'm working in thread ${Thread.currentThread().name}")
 }
 launch(Dispatchers.IO) { // 共享后台线程池里的线程
     println("IO               : I'm working in thread ${Thread.currentThread().name}")
 }
 launch(Dispatchers.Unconfined) { // 不受限的——将工作在主线程中
     println("Unconfined            : I'm working in thread ${Thread.currentThread().name}")
 }
 launch(newSingleThreadContext("MyOwnThread")) { // 将使它获得一个新的线程
     println("newSingleThreadContext: I'm working in thread ${Thread.currentThread().name}")
 }

```
##### 协程的挂起和恢复
> thread 线程之间采取的是竞争 cpu 时间段的方法，谁抢到谁运行，由系统内核控制，对我们来说是不可见不可控的。协程不同，协程之间不用竞争、谁运行、谁挂起、什么时候恢复都是由我们自己控制的。

- 协程执行时， 协程和协程，协程和线程内代码是顺序运行的。
- 协程挂起时，就不会执行了，而是等待挂起完成且线程空闲时才能继续执行。
 - suspend 修饰的方法挂起的是协程本身。
 - await，会阻塞外部协程。适用于多个同级 IO 操作的情况。await()可以返回当前协程的执行结果。

 ```
 runBlocking {
        val deferred1 = async(Dispatchers.Default) {
            println(Thread.currentThread())
            "hello1"
        }

        async(Dispatchers.Default){
            println(Thread.currentThread())
            println("hello2")
            //await()可以返回当前协程的执行结果: hello1
            println(deferred1.await())
        }

    }
```

------

kotlin汇总介绍： [kotlin协程指南](http://www.kotlincn.net/docs/reference/coroutines/coroutines-guide.html)

------

> 工作需要，目前正在写一个kotlin社交项目。项目中用到room存储数据，在线程里通过Dao操作本地数据库，根据Dao数据返回到主线程更新UI。这个线程切换，使用协程来操作，代码简洁易读。所以花点时间了解下！
> 
> Kotlin中的协程也是通过线程池来实现的。而在Kotlin中，在线程之上也建立了在线程中类似于Looper+Handler的机制，让协程可以在多个线程中切换，以及进行数据的传递。
>  [Android子线程切换到UI线程方法总结](https://juejin.im/post/5daa9938e51d4524b95169f6)


