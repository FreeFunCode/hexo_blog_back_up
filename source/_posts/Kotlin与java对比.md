---
title: Kotlin与java对比
date: 2019-09-28 17:48:07
tags: kotlin
categories: 
- Android
- kotlin
---
[点击查看原文](https://juejin.im/post/5d8f2aa4e51d4577ea077ead)

Kotlin与java对比
------------------
> 最近因为工作关系，需要迭代维护公司现有kotlin客服聊天的项目。之前一直使用java，对于kotlin还是比较陌生的。
> 使用kotlin一段时间后，感触还是有点。kotlin比java真是太简洁了，一段代码，刚开始可能按java语法习惯编写，然后根据ide的提示，一步一步优化，缩减代码量，最后发现，代码量的差距很大。最后剩下的代码可能就只剩下了业务逻辑了。
> kotlin结合mvvm架构，项目非常简洁。
> kotlin是基于jvm编程语言，同java一样可以编译成class字节码文件。同java之间可以相互调用。

### 基础语法
--------
#### 1.定义函数
java：
```
public String retrunStr(String str){
        return str;
}
```
kotlin：
```
fun returnStr(str: String): String {
        return str
}
```
--------

#### 2.定义变量
java：
```
public String str1;
public static final String str2 = "HelloWorld";
```
kotlin：
```
var str1: String? = null
val str2 = "HelloWorld"
```
--------

#### 3. 空置判断 ：? 和 !!
java：
```
?
public String retrunStr2(String str) {
        if (str != null) {
            return str1;
        } else {
            return "";
        }
}

!!
public void toLowerStr(String str) {
        try {
            str.toLowerCase();
        } catch (NullPointerException e) {
            e.printStackTrace();
        }
}
```
kotlin：
```
?
fun retrunStr2(str: String?): String {
        return str ?: ""
}

!!
fun toLowerStr(str: String) {
        str!!.toLowerCase()
}
```
--------

#### 4.for循环
java：
```
public void test() {
        List<String> arr = Arrays.asList("java", "kotlin");
        for (int i = 0; i < arr.size(); i++) {
            System.out.println(i);
        }

        for (int i = 0; i < arr.size(); i++) {
            System.out.println(arr.get(i));
        }

        for (Iterator it = arr.iterator(); it.hasNext(); ) {
            System.out.println(it);
        }
}
```
kotlin：
```
fun test() {
        val arr = Arrays.asList("java", "kotlin")
        for (i in arr.indices) {
            println(i)
        }

        for (i in arr.indices) {
            println(arr[i])
        }

        val it = arr.iterator()
        while (it.hasNext()) {
            println(it)
        }

        for(item in arr){
            println(item)
        }
}
```
--------

#### 5.when 表达式
java：
```
public void test1() {
        int item = 3;
        switch (item) {
            case 1:
                System.out.println(1);
                break;
            case 2:
                System.out.println(2);
                break;
            case 3:
                System.out.println(3);
                break;
            default:
                System.out.println(0);
        }
}
```
kotlin：
```
fun test1() {
        val item = 3
        when (item) {
            1 -> println(1)
            2 -> println(2)
            3 -> println(3)
            else -> println(0)
        }
}
```
--------

### 标准函数
--------
#### 1.let
> let 扩展函数是一个作用域函数，当你需要去定义一个变量在一个特定的作用域范围内，可以使用let函数。
```
object.let{
   it.todo()//在函数体内使用it替代object对象去访问其公有的属性和方法
}
```
```
java示例:
------------
	public static final void main(@NotNull String[] args) {
        Intrinsics.checkParameterIsNotNull(args, "args");
        String var2 = "testLet";
        System.out.println(var2.length());//输出 7
        int result = 1000;
        System.out.println(result); //输出 1000
    }

kotlin示例:
------------
	fun main(args: Array<String>) {
        val result = "testLet".let {
            println(it.length) //输出 7
            1000 //在函数块内可以通过 it 指代该对象。
        }
        //返回值为函数块的最后一行或指定return表达式。1000
        println(result) //输出1000
    }    
```
--------
#### 2.with
>with 将某对象作为函数的参数，在函数块内可以通过 this 指代该对象。返回值为函数块的最后一行或指定return表达式。
```
 with(object){
   //todo
 }
```
```
java示例:
------------
	public final void main(@NotNull String[] args) {
        Intrinsics.checkParameterIsNotNull(args, "args");
        User user = new User("Kotlin", 1, "1111111");
        System.out.println("my name is " + user.getName() + ", I am " + user.getAge() + " years old, my phone number is " + user.getPhoneNum());
        //输出 my name is Kotlin, I am 1 years old, my phone number is 1111111
        int result = 1000;
        System.out.println("result: " + result); //输出 1000
    }

	private class User {
        String name;
        Integer age;
        String phoneNum;

        public User(String name, int age, String phoneNum) {
        }

        public String getName() {
            return name;
        }

        public Integer getAge() {
            return age;
        }

        public String getPhoneNum() {
            return phoneNum;
        }

    }

kotlin示例:
------------
	 fun main(args: Array<String>) {
        val user = User("Kotlin", 3, "1111111")
        val result = with(user) {
	        //这里this 指 user,this可以省略
            println("my name is $this.name, I am $age years old, my phone number is $phoneNum")
            //输出 my name is Kotlin, I am 1 years old, my phone number is 1111111
            1000
        }
        //返回值为函数块的最后一行或指定return表达式。1000
        println("result: $result")//输出 1000
    }
    
	class User(name: String, age: Int, phoneNum: String) {
        var name: String
            get() {
                return name
            }
            set(value) {}

        var age: Int
            get() {
                return age
            }
            set(value) {}

        var phoneNum: String
            get() {
                return phoneNum
            }
            set(value) {}
    } 
```
--------

#### 3.run
> run 函数实际上可以说是let和with两个函数的结合体，run函数只接收一个lambda函数为参数，以闭包形式返回，返回值为最后一行的值或者指定的return的表达式。
```
object.run{
	//todo
}
```
```
java示例:
------------
    public final void main(@NotNull String[] args) {
        Intrinsics.checkParameterIsNotNull(args, "args");
        User user = new User("Kotlin", 1, "1111111");
        System.out.println("my name is " + user.getName() + ", I am " + user.getAge() + " years old, my phone number is " + user.getPhoneNum());
        int result = 1000;
        System.out.println("result: " + result);//输出 1000
    }

kotlin示例:
------------
    fun main(args: Array<String>) {
        val user = User("Kotlin", 1, "1111111")
        val result = user.run {
	        //这里this 指 user,this可以省略.
            println("my name is $this.name, I am $age years old, my phone number is $phoneNum")
            1000
        }
        //返回值为最后一行的值或者指定的return的表达式 1000
        println("result: $result")//输出 1000
    }  
```
--------

#### 4.apply
> apply 函数和run函数很像，唯一不同点就是它们各自返回的值不一样，run函数是以闭包形式返回最后一行代码的值，而apply函数的返回的是传入对象的本身。
```
object.apply{
	//todo
}
```
```
java示例:
------------
    public final void main(String[] args) {
        User user = new User("Kotlin", 1, "1111111");
        String var5 = "my name is " + user.getName() + ", I am " + user.getAge() + " years old, my phone number is " + user.getPhoneNum();
        System.out.println(var5);
        String var3 = "result: " + user;
        System.out.println(var3); //打印 user对象
    }

kotlin示例:
------------
    fun main(args: Array<String>) {
        val user = User("Kotlin", 1, "1111111")
        val result = user.apply {
	        //这里this 指 user,this可以省略.
            println("my name is $this.name, I am $age years old, my phone number is $phoneNum")
            1000
        }
        //apply函数的返回的是传入对象的本身 user
        println("result: $result") //打印 user对象
    }
```
--------

#### 5.also
> also 函数的结构实际上和let很像唯一的区别就是返回值的不一样，let是以闭包的形式返回，返回函数体内最后一行的值，如果最后一行为空就返回一个Unit类型的默认值。而also函数返回的则是传入对象的本身。
```
object.also{
	//todo
}
```
```
java示例:
------------
    public void main(String[] args) {
        String var1 = "test";
        System.out.println(var1.length());//输出 4
        System.out.println(var1);//输出 test
    }

kotlin示例:
------------
    fun main(args: Array<String>) {
        val result = "test".also {
	        //it 指的是 "test"
            println(it.length) //输出 4
            1000
        }
        //also函数返回的是传入对象的本身 "test"
        println(result) //输出 test
    }
```
--------

### 函数小结：let,with,run,apply,also函数区别

| 函数名     | 使用方法               | it,this对象  		 |   返回值  | 是否是扩展函数| 适用的场景 |
| :--     |:--                  | :--               |  :-- |:--:|:--|
| let		| object.let{ it.xxx }  |it指代当前对象        |闭包形式返回|     是   |适用于处理不为null的操作场景|
| with		| with(object){ xxx }   |this指代当前对象或者省略|闭包形式返回|     否   |适用于调用同一个类的多个方法时，可以省去类名重复，直接调用类的方法即可，经常用于Android中RecyclerView中onBinderViewHolder中，数据model的属性映射到UI上|
| run       | object.run{ xxx }     |this指代当前对象或者省略|闭包形式返回|     是   |适用于let,with函数任何场景。|
| apply     | object.apply{ xxx }   |this指代当前对象或者省略|返回this   |     是  |1、适用于run函数的任何场景，一般用于初始化一个对象实例的时候，操作对象属性，并最终返回这个对象。<br>2、动态inflate出一个XML的View的时候需要给View绑定数据也会用到.<br>3、一般可用于多个扩展函数链式调用 <br>4、数据model多层级包裹判空处理的问题 |
| also      | object.also{ xxx }    |it指代当前对象		  |返回it    |     是    |适用于let函数的任何场景，一般可用于多个扩展函数链式调用|



参考文章：[Kotlin系列之let、with、run、apply、also函数的使用](https://blog.csdn.net/u013064109/article/details/78786646)