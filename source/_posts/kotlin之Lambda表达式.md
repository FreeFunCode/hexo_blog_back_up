---
title: kotlin之Lambda表达式
date: 2019-10-26 01:01:49
tags: kotlin
categories: 
- Android
- kotlin
---

# kotlin之Lambda表达式

>  Java8中引入，匿名函数。
>  
>  普通的Lambda表达式类似对应kotlin普通函数的声明，而带接收者的lambda表达式则类似对应kotlin的扩展函数。

##### lambda的基本语法
`{ variable -> body_of_function}` 示例：` { x:Int, y:Int -> x + y }`

lambda表达式始终用花括号包围，实参并没有用括号括起来。箭头把实参列表和lambda的函数体隔开

<!-- more -->

##### lambda作为函数的参数传递
示例：`val sum = { x: Int, y: Int -> x + y }`

可以把lambda表达式存储在一个变量中，把这个变量当做普通函数对待，也可以直接写作函数参数。

##### lambda表达式的返回值
```
val isOddNumber = { number: Int ->
        println("number is $number")
        number % 2 == 1
    }
```
lambda表达式返回值总是返回函数体内部最后一行表达式的值

##### invoke 方法
```
fun main(args: Array<String>) { 
     val add= { 
         left: Int, right: Int 
         -> 
         left + right 
     } 
     println(add.invoke(2, 3)) 
 } 
```
一个变量 add，赋值为一个 Lambda 表达式。Lambda 表达式用一对大括号括起来，后面先依次写下参数及其类型，如果没有就不写，接着写下 -> ，这表明后面的是函数体了，函数体的最后一句的表达式结果就是 Lambda 表达式的返回值，比如这里的返回值就是参数求和的结果。后面我们用 () 的形式调用这个 Lambda 表达式，其实这个 () 对应的是 invoke 方法。

##### 结合kotlin 扩展函数使用

[Kotlin与java对比](https://inote.fun/2019/09/28/Kotlin%E4%B8%8Ejava%E5%AF%B9%E6%AF%94/)

-----

注意：语法简化是把双刃剑，简化固然不错，使用简单方便，但是不能滥用，也需要考虑到代码的可读性.上图中Lambda化简成的最简单形式用it这种，一般在多个Lambda嵌套的时候不建议使用，严重造成代码可读性，到最后估计连开发者都不知道it指代什么了。

![kotlinVSjava](/images/kotlin_vs_java.png)