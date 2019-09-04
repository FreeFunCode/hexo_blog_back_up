---
title: 23种设计模式在Android中的应用
date: 2018-07-06 23:37:49
tags: 设计模式
categories: 设计模式
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/java_design.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p>所有江湖偶遇,都是宿命相逢 ----《逆水寒》，只是觉得文案不错，就用了。哈哈！</p>
<h2><strong>一.设计原则：</strong></h2>
<p>单一职责原则（SRP）：任何一个对象都应给只有一个单独的职责(“低耦合，高内聚”)<br>里氏替换原则（LSP）：在任何父类出现的地方都可以用子类替换<br>依赖注入原则（DIP）：要依赖于抽象而不是依赖于具体实现（此原则是开闭原则的基础）<br>接口分离原则（ISP）：不应该强迫他们的客户程序依赖他们不需要的方法<br>迪米特原则（LOD）：一个对象应该对其他对象尽可能少的了解，意思就是降低各个对象之间的耦合。<br>开闭原则（OCP）：一个对对象对扩展是开放的，对修改是关闭的。</p>
<h2>二.23中设计模式</h2>
<p>1.<strong>工厂模式(Factory Pattern)：</strong>在工厂模式中，客户类与工厂来是分开的，消费者任何时候需要产品只需要向工厂请求就好，消费者无需修改就可以接纳新产品。缺点是：当前产品修改是工厂也需要修改。</p>
<p>2.<strong>建造模式(Builder Pattern)：</strong>将产品的内部表象和产品的生成过程分割开来，从而使一个建造过程生成具有不同表象的产品，建造模式使得产品内部表象可以独立的变化，客户不必知道产品的内部细节。建造模式可以强制实行一种分步骤的建造过程。</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('0227c0ad-92ec-4e6d-bd4d-2cbd65fe0531')"><img id="code_img_closed_0227c0ad-92ec-4e6d-bd4d-2cbd65fe0531" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_0227c0ad-92ec-4e6d-bd4d-2cbd65fe0531" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('0227c0ad-92ec-4e6d-bd4d-2cbd65fe0531',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_0227c0ad-92ec-4e6d-bd4d-2cbd65fe0531" class="cnblogs_code_hide">
<pre>AlertDialog.Builder builder  = <span style="color: #0000ff;">new</span> AlertDialog.Builder(<span style="color: #0000ff;">this</span><span style="color: #000000;">);
        builder.setTitle(</span>""<span style="color: #000000;">)
                .setIcon(R.drawable.ic_launcher_foreground)
                .setView(R.layout.activity_main)
                .setOnKeyListener(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> DialogInterface.OnKeyListener() {
                    @Override
                    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">boolean</span> onKey(DialogInterface dialogInterface, <span style="color: #0000ff;">int</span><span style="color: #000000;"> i, KeyEvent keyEvent) {
                        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">false</span><span style="color: #000000;">;
                    }
                })
                .setOnCancelListener(</span><span style="color: #0000ff;">new</span><span style="color: #000000;"> DialogInterface.OnCancelListener() {
                    @Override
                    </span><span style="color: #0000ff;">public</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> onCancel(DialogInterface dialogInterface) {
                        
                    }
                })
                .create().show();</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>3.<strong>工厂方法模式(Abstract Factory Pattern)：</strong>在工厂方法模式中，核心工厂类不再负责所有的产品的创建，而是将具体创建工作交给子类处理，成为一个抽象角色，仅负责给出具体工厂必须实现的接口，而不接触某款产品的实例化细节。</p>
<p>4.<strong>原型模型模式(Prototype Pattern)：</strong>原型模型是通过给定一个原型对象来指明要创建的的对象类型，可以用复制这个对象的方法创建更多的对象，原型模型允许动态的增加或减少产品类。缺点是：每一个类必须配备一个克隆方法。</p>
<p>5.<strong>单例模式（Singleton Pattern）：</strong>确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例单例模式。</p>
<p>- 饿汉式单例</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<div class="cnblogs_code_toolbar">public class Singleton {<br>&nbsp;&nbsp;&nbsp;&nbsp;private Singleton(){}<br>&nbsp;&nbsp;&nbsp;&nbsp;private static Singleton instance = new Singleton();<br>&nbsp;&nbsp;&nbsp;&nbsp;public static Singleton getInstance(){<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return instance;<br>&nbsp;&nbsp;&nbsp;&nbsp;}<br>}</div>





<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>-懒汉式单例</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<div class="cnblogs_code_toolbar">
<p>/**<br>&nbsp;* Singleton helper class for lazily initialization.<br>&nbsp;*<br>&nbsp;* Modeled after frameworks/base/include/utils/Singleton.h<br>&nbsp;*<br>&nbsp;* @hide<br>&nbsp;*/<br>public abstract class Singleton&lt;T&gt; {<br>&nbsp;&nbsp;&nbsp; private T mInstance;</p>
<p>&nbsp;&nbsp;&nbsp; protected abstract T create();</p>
<p>&nbsp;&nbsp;&nbsp; public final T get() {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; synchronized (this) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; if (mInstance == null) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; mInstance = create();<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; return mInstance;<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; }<br>&nbsp;&nbsp;&nbsp; }<br>}</p>





</div>





<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a href="javascript:void(0);" onclick="copyCnblogsCode(this)" title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　饿汉模式是在系统运行起来,在装在类的时候就进行初始化的操作,外部对象使用的时候不需要做任何判断可以直接使用,从效率上来说是优于懒汉模式的.但是对比懒汉模式的延迟加载技术,不管系统用不用该实例,内存都会在最开始的时候创建出来.跟懒汉的时间换空间正好相反,饿汉是空间换时间。</p>
<p>6.<strong>适配器模式(Adapter Pattern)：</strong>把我一个类的接口变化成客户端期望的另一个接口。</p>
<p>Adapter.java Android8.0源码</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('bb2ee639-be31-4b15-845a-77ea4c283247')"><img id="code_img_closed_bb2ee639-be31-4b15-845a-77ea4c283247" class="code_img_closed" src="https://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt=""><img id="code_img_opened_bb2ee639-be31-4b15-845a-77ea4c283247" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('bb2ee639-be31-4b15-845a-77ea4c283247',event)" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="">
<div id="cnblogs_code_open_bb2ee639-be31-4b15-845a-77ea4c283247" class="cnblogs_code_hide">
<pre><span style="color: #008000;">/**</span><span style="color: #008000;">
 * adapter 对象充当 AdapterView 和该View展现数据的桥梁。提供对数据的访问。
 * 还负责每一个 android.view.View 控件的数据集控制。
 * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> android.widget.ArrayAdapter
 * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> android.widget.CursorAdapter
 * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> android.widget.SimpleCursorAdapter
 </span><span style="color: #008000;">*/</span>
<span style="color: #0000ff;">public</span> <span style="color: #0000ff;">interface</span><span style="color: #000000;"> Adapter {
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Register an observer that is called when changes happen to the data used by this adapter.
     * 当这个适配器使用的数据放生变化时候，注册一个观察者
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> observer the object that gets notified when the data set changes.
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> observer 当数据集发生变化，得到通知的对象
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">void</span><span style="color: #000000;"> registerDataSetObserver(DataSetObserver observer);

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Unregister an observer that has previously been registered with this
     * adapter via {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #registerDataSetObserver}.
     * 该方法是注销观察者作用，
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> observer the object to unregister.
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> observer 需要注销的观察者
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">void</span><span style="color: #000000;"> unregisterDataSetObserver(DataSetObserver observer);

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * How many items are in the data set represented by this Adapter.
     * adapter数据集大小
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> Count of items.
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> 返回数据集大小
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">int</span><span style="color: #000000;"> getCount();   
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Get the data item associated with the specified position in the data set.
     * 返回数据集对应索引（position）的数据项对象
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> position Position of the item whose data we want within the adapter's 
     * data set.
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> The data at the specified position.
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    Object getItem(</span><span style="color: #0000ff;">int</span><span style="color: #000000;"> position);
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Get the row id associated with the specified position in the list.
     * 返回数据集对应索引（position）的数据项Id
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> position The position of the item within the adapter's data set whose row id we want.
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> The id of the item at the specified position.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">long</span> getItemId(<span style="color: #0000ff;">int</span><span style="color: #000000;"> position);
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Indicates whether the item ids are stable across changes to the
     * underlying data.
     * 如果相同的ID总是引用相同的对象，则为true。
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> True if the same id always refers to the same object.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> hasStableIds();
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Get a View that displays the data at the specified position in the data set. You can either
     * create a View manually or inflate it from an XML layout file. When the View is inflated, the
     * parent View (GridView, ListView...) will apply default layout parameters unless you use
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.view.LayoutInflater#inflate(int, android.view.ViewGroup, boolean)}
     * to specify a root view and to prevent attachment to the root.
     * 返回数据集中对应索引（position）指定的View。同样也可以创建一个视图或者通过xml布局inflate。
     * 如果是通过inflate获取视图时候，</span><span style="color: #808080;">@param</span><span style="color: #008000;"> parent 可以是GridView 或者ListView等控件。
     * 除非你用{</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.view.LayoutInflater#inflate(int, android.view.ViewGroup, boolean)}指定根视图并防止附着，否则使用默认的布局参数。
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> position The position of the item within the adapter's data set of the item whose view
     *        we want.
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> position 我们想要返回的视图,对应数据集索引（position）
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> convertView The old view to reuse, if possible. Note: You should check that this view
     *        is non-null and of an appropriate type before using. If it is not possible to convert
     *        this view to display the correct data, this method can create a new view.
     *        Heterogeneous lists can specify their number of view types, so that this View is
     *        always of the right type (see {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getViewTypeCount()} and
     *        {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getItemViewType(int)}).
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> convertView 如果可以，重用旧视图。注意：需要检查视图不是null并且是适当的类型。如果不能重用，可以创建一个视图。
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> parent The parent that this view will eventually be attached to
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> parent 根视图
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> A View corresponding to the data at the specified position.
     * 返回呈现数据集索引对应数据项的视图。
     </span><span style="color: #008000;">*/</span><span style="color: #000000;">
    View getView(</span><span style="color: #0000ff;">int</span><span style="color: #000000;"> position, View convertView, ViewGroup parent);

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * An item view type that causes the {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> AdapterView} to ignore the item
     * view. For example, this can be used if the client does not want a
     * particular view to be given for conversion in
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getView(int, View, ViewGroup)}.
     * 
     * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> #getItemViewType(int)
     * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> #getViewTypeCount()
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> IGNORE_ITEM_VIEW_TYPE =<span style="color: #000000;"> AdapterView.ITEM_VIEW_TYPE_IGNORE;
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Get the type of View that will be created by {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getView} for the specified item.
     * 
     * </span><span style="color: #808080;">@param</span><span style="color: #008000;"> position The position of the item within the adapter's data set whose view type we
     *        want.
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> An integer representing the type of View. Two views should share the same type if one
     *         can be converted to the other in {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getView}. Note: Integers must be in the
     *         range 0 to {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getViewTypeCount} - 1. {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #IGNORE_ITEM_VIEW_TYPE} can
     *         also be returned.
     * </span><span style="color: #808080;">@see</span><span style="color: #008000;"> #IGNORE_ITEM_VIEW_TYPE
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">int</span> getItemViewType(<span style="color: #0000ff;">int</span><span style="color: #000000;"> position);
    
    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * &lt;p&gt;
     * Returns the number of types of Views that will be created by
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getView}. Each type represents a set of views that can be
     * converted in {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> #getView}. If the adapter always returns the same
     * type of View for all items, this method should return 1.
     * &lt;/p&gt;
     * &lt;p&gt;
     * This method will only be called when the adapter is set on the {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> AdapterView}.
     * &lt;/p&gt;
     * 
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> The number of types of Views that will be created by this adapter
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">int</span><span style="color: #000000;"> getViewTypeCount();
    
    </span><span style="color: #0000ff;">static</span> <span style="color: #0000ff;">final</span> <span style="color: #0000ff;">int</span> NO_SELECTION =<span style="color: #000000;"> Integer.MIN_VALUE;
 
     </span><span style="color: #008000;">/**</span><span style="color: #008000;">
      * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> true if this adapter doesn't contain any data.  This is used to determine
      * whether the empty view should be displayed.  A typical implementation will return
      * getCount() == 0 but since getCount() includes the headers and footers, specialized
      * adapters might want a different behavior.
      </span><span style="color: #008000;">*/</span>
     <span style="color: #0000ff;">boolean</span><span style="color: #000000;"> isEmpty();

    </span><span style="color: #008000;">/**</span><span style="color: #008000;">
     * Gets a string representation of the adapter data that can help
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.service.autofill.AutofillService} autofill the view backed by the adapter.
     *
     * &lt;p&gt;
     * It should only be set (i.e., non-{</span><span style="color: #808080;">@code</span><span style="color: #008000;"> null} if the values do not represent PII
     * (Personally Identifiable Information - sensitive data such as email addresses,
     * credit card numbers, passwords, etc...). For
     * example, it's ok to return a list of month names, but not a list of usernames. A good rule of
     * thumb is that if the adapter data comes from static resources, such data is not PII - see
     * {</span><span style="color: #808080;">@link</span><span style="color: #008000;"> android.view.ViewStructure#setDataIsSensitive(boolean)} for more info.
     *
     * </span><span style="color: #808080;">@return</span><span style="color: #008000;"> {</span><span style="color: #808080;">@code</span><span style="color: #008000;"> null} by default, unless implementations override it.
     </span><span style="color: #008000;">*/</span>
    <span style="color: #0000ff;">default</span><span style="color: #000000;"> @Nullable CharSequence[] getAutofillOptions() {
        </span><span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
    }
}</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>7.<strong>桥接模式(Bridge Pattern)：</strong>将抽象化和实现化脱耦，使得二者可以独立变化，也就是说，将他们的强关联变成弱关联，即软件系统使用的是组合/聚合关系而不是继承关系，从而使两者可以独立变化。</p>
<p>8.<strong>合成模式(Composite Pattern)：</strong>将对象组合成树形结构以表示“部分-整体”的层次结构，使得用户对单个对象和组合对象的使用具有一致性</p>
<p>9.<strong>装饰者模式(Decorator Pattern)：</strong>装饰者模式以客户端透明的方式扩展对象的功能，是继承方案的一个替代方案，提供了比继承更多的灵活性，动态的的给一个对象添加功能，这些功能又可以动态的撤销。可增加又一些基本功能排列组合产生非常强大的功能。</p>
<p>10.<strong>门面模式(Facade Pattern)：</strong>外部与一个子系统通信必须通过一个门面对象进行，门面模式提供一个高层次的接口，使得子系统更容易使用。每一个子系统只有一个门面类，而且此门面类只有一个实例，单整个系统可以有多个门面类。</p>
<p>11.<strong>享元模式(Flyweight Pattern)：</strong>使用共享对象可有效地支持大量的细粒度对象。</p>
<p>12.<strong>代理模式(Proxy pattern)：</strong>给某一个对象创建一个代理对象，并由代理对象控制源对象的应用。</p>
<p>13.<strong>责任链模式(chain of responsinbleity Pattern)：</strong>使多个对象有机会处理请求，从而避免了请求的发送者和接收者之间的耦合关系 。将这些对象连成一个链，并沿着这条链传递请求，知道有对象处理它为止</p>
<p>14.<strong>命令模式(cmd Pattern)：</strong>将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录请求日志，可以提供命令的撤销和恢复功能。</p>
<p>15<strong>.解释器模式(Interpreter Pattern)：</strong>给定一门语言，定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子。</p>
<p>16.<strong>迭代子模式(Iterator Pattern)：</strong>它提供一种方法访问一个容器对象中各个元素，而又不需要暴露该对象的内部细节。</p>
<p>17.<strong>调停者模式(mediator Pattern )：</strong>调停者模式包装了一系列对象相互作用的方式，使得这些对象不必相互明显作用。从而使他们可以松散偶合。当某些对象之间的作用发生改变时，不会立即影响其他的一些对象之间的作用。保证这些作用可以彼此独立的变化。调停者模式将多对多的相互作用转化为一对多的相互作用。调停者模式将对象的行为和协作抽象化，把对象在小尺度的行为上与其他对象的相互作用分开处理。</p>
<p>18.<strong>备忘录模式(Memento Pattern)：</strong>在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样以后就可将该对象恢复到原来保存的状态。</p>
<p>19.<strong>观察者模式(Observer Pattern)：</strong>定义对象间一种一对多的依赖关系，使得每当一个对象改变状态，则所有依赖于它的对象都会得到通知并被自动更新</p>
<p>20.<strong>状态模式(state Pattern )：</strong>当一个对象在状态改变时允许其改变行为，这个对象看起来像改变了其类。</p>
<p>21.<strong>策略模式(Strategy Pattern)：</strong>定义一组算法，将每个算法都封装起来，并且使他们之间可以互换</p>
<p>22.<strong>模板方法模式(Template Method Pattern)：</strong></p>
<p>23.<strong>访问者模式(Visitor Pattern)：</strong>封装一些作用于某种数据结构中的各种元素，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作</p>
<p><br>参考：<br>https://blog.csdn.net/forwujinwei/article/details/79050044</p>
</div>