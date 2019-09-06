---
title: easyui的 getSelections 与 getSelected 对比区别
date: 2014-07-14 14:11:27
tags: easyui
categories: 前端
---
[点击查看原文](https://www.cnblogs.com/bugzone/p/3842624.html)

<div id="cnblogs_post_body" class="blogpost-body ">

在使用easyui getSelections 与 getSelected中，经常如果不注意这两个的使用，用混的话，如果对这两个属性不了解，找起问题来，都很费事。走过的弯路，做一个记录。

DataGrid组件包括2个方法检索选择行数据：

·&nbsp;getSelected:&nbsp;得到第一个选择行的数据，如果没有选择行则返回null否则返回该记录

·&nbsp;getSelections:得到全部的选择行的数据，如果元素是记录的话，返回数组数据

创建标记

<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_881797" class="syntaxhighlighter  javascript"><div class="toolbar"><span>[?](#)</span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2">`&lt;table id=``"tt"``&gt;&lt;/table&gt;`</div></div></td></tr></tbody></table></div></div>
</div>

创建&nbsp;datagrid

<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_907021" class="syntaxhighlighter  javascript"><div class="toolbar"><span>[?](#)</span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div><div class="line number12 index11 alt1">12</div><div class="line number13 index12 alt2">13</div><div class="line number14 index13 alt1">14</div><div class="line number15 index14 alt2">15</div><div class="line number16 index15 alt1">16</div><div class="line number17 index16 alt2">17</div><div class="line number18 index17 alt1">18</div><div class="line number19 index18 alt2">19</div><div class="line number20 index19 alt1">20</div><div class="line number21 index20 alt2">21</div><div class="line number22 index21 alt1">22</div><div class="line number23 index22 alt2">23</div><div class="line number24 index23 alt1">24</div><div class="line number25 index24 alt2">25</div><div class="line number26 index25 alt1">26</div><div class="line number27 index26 alt2">27</div><div class="line number28 index27 alt1">28</div><div class="line number29 index28 alt2">29</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2">`$(``'#tt'``).datagrid({`</div><div class="line number2 index1 alt1">&nbsp;</div><div class="line number3 index2 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``title:``'Load Data'``,`</div><div class="line number4 index3 alt1">&nbsp;</div><div class="line number5 index4 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``iconCls:``'icon-save'``,`</div><div class="line number6 index5 alt1">&nbsp;</div><div class="line number7 index6 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``width:600,`</div><div class="line number8 index7 alt1">&nbsp;</div><div class="line number9 index8 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``height:250,`</div><div class="line number10 index9 alt1">&nbsp;</div><div class="line number11 index10 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``url:``'datagrid_data.json'``,`</div><div class="line number12 index11 alt1">&nbsp;</div><div class="line number13 index12 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``columns:[[`</div><div class="line number14 index13 alt1">&nbsp;</div><div class="line number15 index14 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'itemid'``,title:``'Item ID'``,width:80},`</div><div class="line number16 index15 alt1">&nbsp;</div><div class="line number17 index16 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'productid'``,title:``'Product ID'``,width:80},`</div><div class="line number18 index17 alt1">&nbsp;</div><div class="line number19 index18 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'listprice'``,title:``'List Price'``,width:80,align:``'right'``},`</div><div class="line number20 index19 alt1">&nbsp;</div><div class="line number21 index20 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'unitcost'``,title:``'Unit Cost'``,width:80,align:``'right'``},`</div><div class="line number22 index21 alt1">&nbsp;</div><div class="line number23 index22 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'attr1'``,title:``'Attribute'``,width:100},`</div><div class="line number24 index23 alt1">&nbsp;</div><div class="line number25 index24 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;``{field:``'status'``,title:``'Status'``,width:60}`</div><div class="line number26 index25 alt1">&nbsp;</div><div class="line number27 index26 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``]]`</div><div class="line number28 index27 alt1">&nbsp;</div><div class="line number29 index28 alt2">`});`</div></div></td></tr></tbody></table></div></div>
</div>

&nbsp;

用法演示

得到选择行数据：

<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_692770" class="syntaxhighlighter  javascript"><div class="toolbar"><span>[?](#)</span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2">`var` `row = $(``'#tt'``).datagrid(``'getSelected'``);`</div><div class="line number2 index1 alt1">&nbsp;</div><div class="line number3 index2 alt2">`if` `(row){`</div><div class="line number4 index3 alt1">&nbsp;</div><div class="line number5 index4 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``alert(``'Item ID:'``+row.itemid+``"\nPrice:"``+row.listprice);`</div><div class="line number6 index5 alt1">&nbsp;</div><div class="line number7 index6 alt2">`}`</div></div></td></tr></tbody></table></div></div>
</div>

&nbsp;

得到全部选择行的itemid:

<div class="cnblogs_Highlighter sh-gutter">
<div><div id="highlighter_805308" class="syntaxhighlighter  javascript"><div class="toolbar"><span>[?](#)</span></div><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter"><div class="line number1 index0 alt2">1</div><div class="line number2 index1 alt1">2</div><div class="line number3 index2 alt2">3</div><div class="line number4 index3 alt1">4</div><div class="line number5 index4 alt2">5</div><div class="line number6 index5 alt1">6</div><div class="line number7 index6 alt2">7</div><div class="line number8 index7 alt1">8</div><div class="line number9 index8 alt2">9</div><div class="line number10 index9 alt1">10</div><div class="line number11 index10 alt2">11</div></td><td class="code"><div class="container"><div class="line number1 index0 alt2">`var` `ids = [];`</div><div class="line number2 index1 alt1">&nbsp;</div><div class="line number3 index2 alt2">`var` `rows = $(``'#tt'``).datagrid(``'getSelections'``);`</div><div class="line number4 index3 alt1">&nbsp;</div><div class="line number5 index4 alt2">`for``(``var` `i=0; i&lt;rows.length; i++){`</div><div class="line number6 index5 alt1">&nbsp;</div><div class="line number7 index6 alt2">`&nbsp;&nbsp;&nbsp;&nbsp;``ids.push(rows[i].itemid);`</div><div class="line number8 index7 alt1">&nbsp;</div><div class="line number9 index8 alt2">`}`</div><div class="line number10 index9 alt1">&nbsp;</div><div class="line number11 index10 alt2">`alert(ids.join(``'\n'``));`</div></div></td></tr></tbody></table></div></div>
</div>

<span style="color: #ff0000;">&nbsp;注意：如果你其实应该用var rows = $('#tt').datagrid('getSelections');但是误使用的是var row = $('#tt').datagrid('getSelected');则取出来的row.length是空值。很郁闷的！</span>

</div>