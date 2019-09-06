---
title: mongodb在windows下安装教程
date: 2014-08-24 02:10:01
tags: mongodb
categories: 前端
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/mongod.html)

<div id="cnblogs_post_body" class="blogpost-body ">
    <p><span style="color: #ff0000;"><strong>原文来自：http://www.cnblogs.com/lecaf/archive/2013/08/23/mongodb.html</strong></span></p>
<p><span style="color: #ff0000;"><strong>红字为本人加</strong></span></p>
<p><strong>一、首先安装mongodb</strong></p>
<p>1.下载地址：<a href="http://www.mongodb.org/downloads">http://www.mongodb.org/downloads</a></p>
<p>2.解压缩到自己想要安装的目录，比如d:\mongodb</p>
<p>3.创建文件夹d:\mongodb\data\db、d:\mongodb\data\log，分别用来安装db和日志文件，在log文件夹下创建一个日志文件MongoDB.log，即d:\mongodb\data\log\MongoDB.log</p>
<p>4.运行cmd.exe进入dos命令界面，执行下列命令</p>
<p>　　&gt; cd d:\mongodb\bin</p>
<p>　　&gt; d:\mongodb\bin&gt;mongod -dbpath "d:\mongodb\data\db"</p>
<p><img src="https://images0.cnblogs.com/blog/203292/201308/21110329-868b0d1fb023479f9a605fc8353515f2.png" alt=""></p>
<p>　看到类似的信息，则说明启动成功，默认MongoDB监听的端口是27017，mysql的是3306</p>
<p>5.测试连接<span style="color: #ff0000;">（这一步可以放到第6步后测试，因为安装后默认是关闭的.....需要手动启动下 NET START MongoDB）</span></p>
<p>　新开一个cmd窗口，进入mongodb的bin目录，输入mongo或者mongo.exe，出现如下信息说明测试通过，此时我们已经进入了test这个数据库，如何进入其他数据库下面会说。</p>
<p>　<img src="https://images0.cnblogs.com/blog/203292/201308/21111025-91a3b6a9bde844688300928f0a9ea26f.png" alt=""></p>
<p>　输入exit或者ctrl+C可退出。</p>
<p>&nbsp;6.当mongod.exe被关闭时，mongo.exe 就无法连接到数据库了，因此每次想使用mongodb数据库都要开启mongod.exe程序，所以比较麻烦，此时我们可以将<span style="line-height: 1.5;">MongoDB安装为windows服务</span></p>
<p><span style="line-height: 1.5;">　还是运行cmd，进入bin文件夹，执行下列命令</span></p>
<p><span style="line-height: 1.5;">　&gt; d:\mongodb\bin&gt;mongod --dbpath "d:\mongodb\data\db" --logpath "d:\mongodb\data\log\MongoDB.log" --install --serviceName "MongoDB"</span></p>
<p>　这里MongoDB.log就是开始建立的日志文件，--serviceName "MongoDB" 服务名为MongoDB</p>
<p>　接着启动mongodb服务</p>
<p>　&gt;&nbsp;d:\mongodb\bin&gt;NET START MongoDB</p>
<p>　<img src="https://images0.cnblogs.com/blog/203292/201308/21161507-c374a9f43c1c4c75aeb9cc4ff83ef463.jpg" alt=""></p>
<p>　打开任务管理器，可以看到进程已经启动</p>
<p>7.关闭服务和删除进程</p>
<p>　&gt; d:\mongodb\bin&gt;NET&nbsp;stop MongoDB&nbsp;&nbsp; (关闭服务)</p>
<p>　&gt; d:\mongodb\bin&gt;mongod --dbpath "d:\mongodb\data\db" --logpath "d:\mongodb\data\log\MongoDB.log" --remove --serviceName "MongoDB"&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; (删除，注意不是--install了）</p>
<p>&nbsp;</p>
<p><strong>二、使用mongodb</strong></p>
<p>1.常用的命令</p>
<ul>
<li>show dbs &nbsp; &nbsp;显示数据库列表</li>
<li>use dbname &nbsp; &nbsp;进入dbname数据库，大小写敏感，没有这个数据库也不要紧</li>
<li>show collections &nbsp; &nbsp;显示数据库中的集合，相当于表格</li>
</ul>
<p>2.创建&amp;新增</p>
<ul>
<li>db.users.save({"name":"lecaf"}) &nbsp; &nbsp;<em>创建了名为users的集合，并新增了一条{"name":"lecaf"}的数据</em></li>
<li>db.users.insert({"name":"ghost", "age":10}) &nbsp; &nbsp;<em>在users集合中插入一条新数据，，如果没有users这个集合，mongodb会自动创建</em></li>
<li>save()和insert()也存在着些许区别：若新增的数据主键已经存在，insert()会不做操作并提示错误，而save() 则更改原来的内容为新内容。</li>
<ul>
<li>存在数据：{ _id : 1, " name " : " n1 "} ，_id是主键</li>
<li>insert({ _id : 1, " name " : " n2 " }) &nbsp; &nbsp;<em>会提示错误</em></li>
<li>save({ _id : 1, " name " : " n2 " })<em> &nbsp; &nbsp; 会把 n1 改为 &nbsp;n2 ，有update的作用。</em></li>
</ul>
</ul>
<p>3.删除</p>
<ul>
<li>db.users.remove() &nbsp; &nbsp;<em>删除users集合下所有数据</em></li>
<li>db.users.remove({"name": "lecaf"}) &nbsp; &nbsp;<em>删除users集合下name=lecaf的数据</em></li>
<li>db.users.drop()或db.runCommand({"drop","users"}) &nbsp; &nbsp;<em>删除集合users</em></li>
<li>db.runCommand({"dropDatabase": 1}) &nbsp; <em>&nbsp;删除当前数据库</em></li>
</ul>
<p>4.查找</p>
<ul>
<li>db.users.find() &nbsp; &nbsp;<em>查找users集合中所有数据</em></li>
<li>db.users.findOne() &nbsp; &nbsp;<em>查找users集合中的第一条数据</em></li>
</ul>
<p>5.修改</p>
<ul>
<li>db.users.update({"name":"lecaf"}, {"age":10}) &nbsp; &nbsp;<em>修改name=lecaf的数据为age=10，第一个参数是查找条件，第二个参数是修改内容，除了主键，其他内容会被第二个参数的内容替换，主键不能修改，如图</em></li>
</ul>
<p>　<img src="https://images0.cnblogs.com/blog/203292/201308/22143209-cddea033063e480bb1bd4cc5dfdf9f4e.jpg" alt=""></p>
<p><strong>三、高级应用</strong></p>
<p>1.条件查找</p>
<ul>
<li>db.collection.find({ "key" : value }) &nbsp; &nbsp;查找key=value的数据</li>
<li>db.collection.find({ "key" : { $gt: value }&nbsp;}) &nbsp; &nbsp;key &gt; value</li>
<li>db.collection.find({ "key" :&nbsp;{ $lt: value }&nbsp;}) &nbsp; &nbsp;key &lt; value</li>
<li>db.collection.find({ "key" :&nbsp;{ $gte: value }&nbsp;}) &nbsp; &nbsp;key &gt;= value</li>
<li>db.collection.find({ "key" :&nbsp;{&nbsp;$lte: value }&nbsp;}) &nbsp; &nbsp;key &lt;= value</li>
<li>db.collection.find({ "key" :&nbsp;{ $gt: value1 , $lt: value2&nbsp;}&nbsp;}) &nbsp; &nbsp;value1 &lt; key &lt;value2</li>
<li>db.collection.find({ "key" :&nbsp;{&nbsp;$ne: value }&nbsp;}) &nbsp; &nbsp;key &lt;&gt; value</li>
<li>db.collection.find({ "key"&nbsp;: { $mod : [ 10 , 1 ] } }) &nbsp; &nbsp;取模运算，条件相当于key % 10 == 1 即key除以10余数为1的</li>
<li>db.collection.find({&nbsp;"key"&nbsp;: { $nin: [ 1, 2, 3 ] } }) &nbsp; &nbsp;不属于，条件相当于key的值不属于[ 1, 2, 3 ]中任何一个</li>
<li>db.collection.find({&nbsp;"key"&nbsp;: { $in: [ 1, 2, 3 ] } }) &nbsp; &nbsp;属于，条件相当于key等于[ 1, 2, 3 ]中任何一个</li>
<li>db.collection.find({ "key"&nbsp;: { $size: 1 } }) &nbsp; &nbsp;$size 数量、尺寸，条件相当于key的值的数量是1（key必须是数组，一个值的情况不能算是数量为1的数组）</li>
<li>db.collection.find({ "key"&nbsp;: { $exists : true|false } }) &nbsp; &nbsp;$exists 字段存在，true返回存在字段key的数据，false返回不存在字度key的数据</li>
<li>db.collection.find({ "key": /^val.*val$/i }) &nbsp; &nbsp;正则，类似like；“i”忽略大小写，“m”支持多行</li>
<li>db.collection.find({ $or : [{a : 1}, {b : 2} ] }) &nbsp; &nbsp;$or或 （注意：MongoDB 1.5.3后版本可用），符合条件a=1的或者符合条件b=2的数据都会查询出来</li>
<li>db.collection.find({ "key": value , $or : [{ a : 1 } , { b : 2 }] }) &nbsp; &nbsp;符合条件key=value&nbsp;，同时符合其他两个条件中任意一个的数据</li>
<li>db.collection.find({ "key.subkey" :value }) &nbsp; &nbsp;内嵌对象中的值匹配，注意："key.subkey"必须加引号</li>
<li>db.collection.find({ "key": { $not : /^val.*val$/i&nbsp;} }) &nbsp; &nbsp;这是一个与其他查询条件组合使用的操作符，不会单独使用。上述查询条件得到的结果集加上$not之后就能获得相反的集合。</li>
</ul>
<p>2.排序</p>
<ul>
<li>db.collection.find().sort({ "key1" : -1 ,"key2" : 1 }) &nbsp; &nbsp;这里的1代表升序，-1代表降序</li>
</ul>
<p>3.其他</p>
<ul>
<li>db.collection.find().limit(5) &nbsp; &nbsp;控制返回结果数量，如果参数是0，则当作没有约束，limit()将不起作用</li>
<li>db.collection.find().skip(5) &nbsp; &nbsp;控制返回结果跳过多少数量，如果参数是0，则当作没有约束，skip()将不起作用，或者说跳过了0条</li>
<li>db.collection.find().skip(5).limit(5) &nbsp; &nbsp;可用来做分页，跳过5条数据再取5条数据</li>
<li>db.collection.find().count(true) &nbsp; &nbsp;count()返回结果集的条数</li>
<li>db.collection.find().skip(5).limit(5).count(true) &nbsp; &nbsp;在加入skip()和limit()这两个操作时，要获得实际返回的结果数，需要一个参数true，否则返回的是符合查询条件的结果总</li>
</ul>
</div>