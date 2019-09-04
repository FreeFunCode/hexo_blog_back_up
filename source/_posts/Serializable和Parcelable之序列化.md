---
title: Serializable和Parcelable之序列化
date: 2018-07-22 00:28:23
tags: 序列化
categories: Android
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/Serializable_Parcelable.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <div>序列化，表示将一个对象转换成可存储或可传输的状态。序列化后的对象可以在网络上进行传输，也可以存储到本地。
<h3>&nbsp;一.Serializable和Parcelable介绍</h3>
&nbsp;Android中Intent如果要传递类对象，可以通过两种方式实现。<br>&nbsp;&nbsp;方式一：Serializable，要传递的类实现Serializable接口传递对象，<br>&nbsp;&nbsp;方式二：Parcelable，要传递的类实现Parcelable接口传递对象。<br>&nbsp;&nbsp;<br>&nbsp;Serializable（Java自带）： <br>&nbsp;&nbsp;Serializable是序列化的意思，表示将一个对象转换成可存储或可传输的状态。序列化后的对象可以在网络上进行传输，也可以存储到本地。只需要实现Serializable接口就可以，是由系统来完成序列化和反序列化操作。<br>&nbsp;Parcelable（android 专用）： <br>&nbsp;&nbsp;除了Serializable之外，使用Parcelable也可以实现相同的效果， <br>&nbsp;&nbsp;不过不同于将对象进行序列化，Parcelable方式的实现原理是将一个完整的对象进行分解， <br>&nbsp;&nbsp;而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了。 &nbsp;
<h3>&nbsp;二.Serializable和Parcelable对比&nbsp;</h3>

&nbsp;1.在使用内存的时候，Parcelable比Serializable性能高，所以推荐使用Parcelable。<br>&nbsp;2.Serializable在序列化的时候会产生大量的临时变量，从而引起频繁的GC。<br>&nbsp;3.Parcelable无法将数据进行持久化,因此在将数据保存在磁盘的时候,仍然需要使用后者,因为前者无法很好的将数据进行持久化.(原因是在不同的Android版本当中,Parcelable可能会不同,因此数据的持久化方面仍然是使用Serializable)<br>&nbsp;4.Parcelable 不仅仅需要声明,还需要实现内部的相应方法.writeToParcel;重写describeContents方法,默认值为0;Public static final Parcelable.Creator&lt;T&gt;CREATOR = new CREATOR&lt;T&gt;(){...}<br>&nbsp;5.Parcelable是以Ibinder作为信息载体的.在内存上的开销比较小,因此在内存之间进行数据传递的时候,Android推荐使用Parcelable,既然是内存方面比价有优势,那么自然就要优先选择.<br>&nbsp;6.在读写数据的时候,Parcelable是在内存中直接进行读写,而Serializable是通过使用IO流的形式将数据读写入在硬盘上.&nbsp;</div>
<div>
<h3>&nbsp;三.序列化 运用场景</h3>

</div>
<div>&nbsp;注意：不想对部分关键字进行序列化,可以使用transient关键字来修饰以及static修饰.<br>&nbsp;1.永久的保存对象数据(将对象数据保存在文件当中,或者是磁盘中<br>&nbsp;2.通过序列化操作将对象数据在网络上进行传输(由于网络传输是以字节流的方式对数据进行传输的.因此序列化的目的是将对象数据转换成字节流的形式)<br>&nbsp;3.将对象数据在进程之间进行传递(Activity之间传递对象数据时,需要在当前的Activity中对对象数据进行序列化操作.在另一个Activity中需要进行反序列化操作讲数据取出)<br>&nbsp;4.Java平台允许我们在内存中创建可复用的Java对象，但一般情况下，只有当JVM处于运行时，这些对象才可能存在，即，这些对象的生命周期不会比JVM的生命周期更长（即每个对象都在JVM中）但在现实应用中，就可能要停止JVM运行，但有要保存某些指定的对象，并在将来重新读取被保存的对象。这是Java对象序列化就能够实现该功能。（可选择入数据库、或文件的形式保存）<br>&nbsp;5.序列化对象的时候只是针对变量进行序列化,不针对方法进行序列化.<br>&nbsp;6.在Intent之间,基本的数据类型直接进行相关传递即可,但是一旦数据类型比较复杂的时候,就需要进行序列化操作了.&nbsp;
<h3>&nbsp;四.总结：</h3>

&nbsp;&nbsp;Java应用程序中有Serializable来实现序列化操作,Android中有Parcelable来实现序列化操作,相关的性能也作出了比较,因此<strong>在Android中除了对数据持久化的时候需要使用到Serializable来实现序列化操作,其他的时候我们仍然需要使用Parcelable来实现序列化操作</strong>,因为在Android中效率并不是最重要的,而是内存,通过比较Parcelable在效率和内存上都要优秀与Serializable.<br>&nbsp;<br>&nbsp;<br>&nbsp;附上Parcelable.java源码 主要describeContents(),writeToParcel(Parcel dest, @WriteFlags int flags),interface Creator&lt;T&gt;{}</div>
<div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">interface</span><span style="color: #000000;"> Parcelable {
    
        </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
         * Describe the kinds of special objects contained in this Parcelable
         * instance's marshaled representation. For example, if the object will
         * include a file descriptor in the output of {@link #writeToParcel(Parcel, int)},
         * the return value of this method must include the
         * {@link #CONTENTS_FILE_DESCRIPTOR} bit.
         *  
         * @return a bitmask indicating the set of special object types marshaled
         * by this Parcelable object instance.
         </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">public</span> @ContentsFlags <span style="color: #0000ff;">int</span><span style="color: #000000;"> describeContents();
        
        </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
         * Flatten this object in to a Parcel.
         * 
         * @param dest The Parcel in which the object should be written.
         * @param flags Additional flags about how the object should be written.
         * May be 0 or {@link #PARCELABLE_WRITE_RETURN_VALUE}.
         </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span> writeToParcel(Parcel dest, @WriteFlags <span style="color: #0000ff;">int</span><span style="color: #000000;"> flags);
        
        </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
         * Interface that must be implemented and provided as a public CREATOR
         * field that generates instances of your Parcelable class from a Parcel.
         </span><span style="color: #008000;">*/</span>
        <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">interface</span> Creator&lt;T&gt;<span style="color: #000000;"> {
            </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
             * Create a new instance of the Parcelable class, instantiating it
             * from the given Parcel whose data had previously been written by
             * {@link Parcelable#writeToParcel Parcelable.writeToParcel()}.
             * 
             * @param source The Parcel to read the object's data from.
             * @return Returns a new instance of the Parcelable class.
             </span><span style="color: #008000;">*/</span>
            <span style="color: #0000ff;">public</span><span style="color: #000000;"> T createFromParcel(Parcel source);
            
            </span><span style="color: #008000;">/*</span><span style="color: #008000;">*
             * Create a new array of the Parcelable class.
             * 
             * @param size Size of the array.
             * @return Returns an array of the Parcelable class, with every entry
             * initialized to null.
             </span><span style="color: #008000;">*/</span>
            <span style="color: #0000ff;">public</span> T[] newArray(<span style="color: #0000ff;">int</span><span style="color: #000000;"> size);
        }
    
    
    }</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>[1]&nbsp;序列化Serializable和Parcelable的理解和区别 https://blog.csdn.net/Double2hao/article/details/70145747</p>
<p>[2] Android中Serializable和Parcelable序列化对象详解 https://www.cnblogs.com/yezhennan/p/5527506.html</p>
</div>
</div>
