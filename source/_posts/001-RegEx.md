---
title: 《正则表达式》知识点汇总摘录
date: 2014-05-07 16:17:16
tags: 正则表达式
categories: 知识点
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/theday140507.html)
<div id="cnblogs_post_body" class="blogpost-body ">

　　<span style="text-decoration: line-through; font-size: 12px;">开园一个月了，但一直没有抽出多少时间，就算有时间，也不知道怎么组织语言记（不是写，是记，写是一个创造的过程，像我这等程序猿，猿嘴吐不出象牙！）点东西！翻翻过去手头整理的一些知识点杂记，然后再结合网上的一些，今天对正则表达式在做一个个人的知识汇总摘录吧！程序员都是共产主义者，一点不假！大部分时候我们只是互联网的搬运工，堆砌者（扯远了，当然成为互联网创造者一直使我们的目标！）。</span>

<div class="para">　　正则表达式是对[字符](http://baike.baidu.com/view/263416.htm)串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。</div>
<div class="para">给定一个正则表达式和另一个字符串，我们可以达到如下的目的：</div>

### 　　　　1. 给定的字符串是否符合正则表达式的过滤逻辑（称作“匹配”）；

### 　　　　2. 可以通过正则表达式，从字符串中获取我们想要的特定部分。

一、首先列出正则表达式是的元字符，只有知道每一个零件的含义以及作用，我们才能灵活的运用这些零件造成汽车来！

<table class="table-view log-set-param" style="width: 773px; height: 1269px;">
<tbody>
<tr>
<td align="left" valign="center" width="75">
<div class="para"><span style="color: #00ccff;">元字符</span></div>
</td>
<td align="left" valign="center" width="658">
<div class="para">描述</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para"><span style="font-size: 12px;">\</span></div>
</td>
<td align="left" valign="center" width="658">
<div class="para">将下一个字符标记为一个特殊字符、或一个原义字符、或一个向后引用、或一个八进制转义符。例如，“\\n”匹配\n。“\n”匹配换行符。序列“\\”匹配“\”而“\(”则匹配“(”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">^</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配输入字符串的开始位置。如果设置了RegExp对象的Multiline属性，^也匹配“\n”或“\r”之后的位置。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">$</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配输入字符串的结束位置。如果设置了RegExp对象的Multiline属性，$也匹配“\n”或“\r”之前的位置。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">*</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配前面的子表达式零次或多次(大于等于0次)。例如，zo*能匹配“z”以及“zoo”。*等价于{0,}。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">+</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配前面的子表达式一次或多次(大于等于1次）。例如，“zo+”能匹配“zo”以及“zoo”，但不能匹配“z”。+等价于{1,}。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">?</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配前面的子表达式零次或一次。例如，“do(es)?”可以匹配“does”或“does”中的“do”。?等价于{0,1}。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">{n}</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">n是一个非负整数。匹配确定的n次。例如，“o{2}”不能匹配“Bob”中的“o”，但是能匹配“food”中的两个o。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">{n,}</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">n是一个非负整数。至少匹配n次。例如，“o{2,}”不能匹配“Bob”中的“o”，但能匹配“foooood”中的所有o。“o{1,}”等价于“o+”。“o{0,}”则等价于“o*”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">{n,m}</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">m和n均为非负整数，其中n&lt;=m。最少匹配n次且最多匹配m次。例如，“o{1,3}”将匹配“fooooood”中的前三个o。“o{0,1}”等价于“o?”。请注意在逗号和两个数之间不能有空格。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">?</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">当该字符 紧跟在任何一个其他限制符（*,+,?，{n}，{n,}，{n,m}）后面时，匹配模式是非贪婪的。非贪婪模式尽可能少的匹配所搜索的字符串，而默认的 贪婪模式则尽可能多的匹配所搜索的字符串。例如，对于字符串“oooo”，“o?”将匹配单个“o”，而“o+”将匹配所有“o”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">.点</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配除“\n”之外的任何单个字符。要匹配包括“\n”在内的任何字符，请使用像“[\s\S]”的模式。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配pattern并获取这一匹配。所获取的匹配可以从产生的Matches集合得到，在VBScript中使用SubMatches集合，在JScript中则使用$0…$9属性。要匹配圆括号字符，请使用“\(”或“\)”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(?:pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配pattern但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用或字符“(|)”来组合一个模式的各个部分是很有用。例如“industr(?:y|ies)”就是一个比“industry|industries”更简略的表达式。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(?=pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">正向肯定 预查，在任何匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例 如，“Windows(?=95|98|NT|2000)”能匹配“Windows2000”中的“Windows”，但不能匹配 “Windows3.1”中的“Windows”。预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从 包含预查的字符之后开始。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(?!pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">正向否定 预查，在任何不匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。例如 “Windows(?!95|98|NT|2000)”能匹配“Windows3.1”中的“Windows”，但不能匹配“Windows2000”中 的“Windows”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(?&lt;=pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">反向肯定预查，与正向肯定预查类似，只是方向相反。例如，“(?&lt;=95|98|NT|2000)Windows”能匹配“2000Windows”中的“Windows”，但不能匹配“3.1Windows”中的“Windows”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">(?&lt;!pattern)</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">反向否定预查，与正向否定预查类似，只是方向相反。例如“(?&lt;!95|98|NT|2000)Windows”能匹配“3.1Windows”中的“Windows”，但不能匹配“2000Windows”中的“Windows”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">x|y</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配x或y。例如，“z|food”能匹配“z”或“food”。“(z|f)ood”则匹配“zood”或“food”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">[xyz]</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">字符集合。匹配所包含的任意一个字符。例如，“[abc]”可以匹配“plain”中的“a”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">[^xyz]</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">负值字符集合。匹配未包含的任意字符。例如，“[^abc]”可以匹配“plain”中的“plin”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">[a-z]</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">字符范围。匹配指定范围内的任意字符。例如，“[a-z]”可以匹配“a”到“z”范围内的任意小写字母字符。</div>
<div class="para">注意:只有连字符在字符组内部时,并且出两个字符之间时,才能表示字符的范围; 如果出字符组的开头,则只能表示连字符本身.</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">[^a-z]</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">负值字符范围。匹配任何不在指定范围内的任意字符。例如，“[^a-z]”可以匹配任何不在“a”到“z”范围内的任意字符。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">\b</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配一个单词边界，也就是指单词和空格间的位置。例如，“er\b”可以匹配“never”中的“er”，但不能匹配“verb”中的“er”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">\B</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配非单词边界。“er\B”能匹配“verb”中的“er”，但不能匹配“never”中的“er”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">\cx</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配由x指明的控制字符。例如，\cM匹配一个Control-M或回车符。x的值必须为A-Z或a-z之一。否则，将c视为一个原义的“c”字符。</div>
</td>
</tr>
<tr>
<td align="left" valign="center" width="75">
<div class="para">\d</div>
</td>
<td align="left" valign="center" width="658">
<div class="para">匹配一个数字字符。等价于[0-9]。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\D</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个非数字字符。等价于[^0-9]。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\f</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个换页符。等价于\x0c和\cL。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\n</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个换行符。等价于\x0a和\cJ。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\r</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个回车符。等价于\x0d和\cM。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\s</div>
</td>
<td align="left" valign="center">
<div class="para">匹配任何空白字符，包括空格、制表符、换页符等等。等价于[ \f\n\r\t\v]。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\S</div>
</td>
<td align="left" valign="center">
<div class="para">匹配任何非空白字符。等价于[^ \f\n\r\t\v]。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\t</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个制表符。等价于\x09和\cI。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\v</div>
</td>
<td align="left" valign="center">
<div class="para">匹配一个垂直制表符。等价于\x0b和\cK。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\w</div>
</td>
<td align="left" valign="center">
<div class="para">匹配包括下划线的任何单词字符。等价于“[A-Za-z0-9_]”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\W</div>
</td>
<td align="left" valign="center">
<div class="para">匹配任何非单词字符。等价于“[^A-Za-z0-9_]”。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\xn</div>
</td>
<td align="left" valign="center">
<div class="para">匹配n，其中n为十六进制转义值。十六进制转义值必须为确定的两个数字长。例如，“\x41”匹配“A”。“\x041”则等价于“\x04&amp;1”。正则表达式中可以使用ASCII编码。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\num</div>
</td>
<td align="left" valign="center">
<div class="para">匹配num，其中num是一个正整数。对所获取的匹配的引用。例如，“(.)\1”匹配两个连续的相同字符。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\n</div>
</td>
<td align="left" valign="center">
<div class="para">标识一个八进制转义值或一个向后引用。如果\n之前至少n个获取的子表达式，则n为向后引用。否则，如果n为八进制数字（0-7），则n为一个八进制转义值。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\nm</div>
</td>
<td align="left" valign="center">
<div class="para">标识一个八进制转义值或一个向后引用。如果\nm之前至少有nm个获得子表达式，则nm为向后引用。如果\nm之前至少有n个获取，则n为一个后跟文字m的向后引用。如果前面的条件都不满足，若n和m均为八进制数字（0-7），则\nm将匹配八进制转义值nm。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\nml</div>
</td>
<td align="left" valign="center">
<div class="para">如果n为八进制数字（0-7），且m和l均为八进制数字（0-7），则匹配八进制转义值nml。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">
<div class="para">\un</div>
</td>
<td align="left" valign="center">
<div class="para">匹配n，其中n是一个用四个十六进制数字表示的Unicode字符。例如，\u00A9匹配版权符号（&amp;copy;）。</div>
</td>
</tr>
<tr>
<td align="left" valign="center">\&lt; \&gt;</td>
<td align="left" valign="center">匹配词（word）的开始（\&lt;）和结束（\&gt;）。例如正则表达式\&lt;the\&gt;能够匹配字符串"for the wise"中的"the"，但是不能匹配字符串"otherwise"中的"the"。注意：这个元字符不是所有的软件都支持的。</td>
</tr>
<tr>
<td align="left" valign="center">\( \)</td>
<td align="left" valign="center">将 \( 和 \) 之间的表达式定义为“组”（group），并且将匹配这个表达式的字符保存到一个临时区域（一个正则表达式中最多可以保存9个），它们可以用 \1 到\9 的符号来引用。</td>
</tr>
<tr>
<td align="left" valign="center">|</td>
<td align="left" valign="center">将两个匹配条件进行逻辑“或”（Or）运算。例如正则表达式(him|her) 匹配"it belongs to him"和"it belongs to her"，但是不能匹配"it belongs to them."。注意：这个元字符不是所有的软件都支持的。</td>
</tr>
<tr>
<td align="left" valign="center">+</td>
<td align="left" valign="center">匹配1或多个正好在它之前的那个字符。例如正则表达式9+匹配9、99、999等。注意：这个元字符不是所有的软件都支持的。</td>
</tr>
<tr>
<td align="left" valign="center">?</td>
<td align="left" valign="center">匹配0或1个正好在它之前的那个字符。注意：这个元字符不是所有的软件都支持的。</td>
</tr>
<tr>
<td align="left" valign="center">{i} {i,j}</td>
<td align="left" valign="center">匹配指定数目的字符，这些字符是在它之前的表达式定义的。例如正则表达式A[0-9]{3} 能够匹配字符"A"后面跟着正好3个数字字符的串，例如A123、A348等，但是不匹配A1234。而正则表达式[0-9]{4,6} 匹配连续的任意4个、5个或者6个数字</td>
</tr>
</tbody>
</table>

二、了解了这些元字符后，我们就可以造车轱辘了！接下来介绍一些常用的正则表达式来做验证！！

<span style="color: #0000ff;">　整数或者小数：^[0-9]+\.{0,1}[0-9]{0,2}$</span>
<span style="color: #0000ff;">　　整数或者小数：^[0-9]+\.{0,1}[0-9]{0,2}$</span>
<span style="color: #0000ff;">　　只能输入数字："^[0-9]*$"。</span>
<span style="color: #0000ff;">　　只能输入n位的数字："^\d{n}$"。</span>
<span style="color: #0000ff;">　　只能输入至少n位的数字："^\d{n,}$"。</span>
<span style="color: #0000ff;">　　只能输入m~n位的数字：。"^\d{m,n}$"</span>
<span style="color: #0000ff;">　　只能输入零和非零开头的数字："^(0|[1-9][0-9]*)$"。</span>
<span style="color: #0000ff;">　　只能输入有两位小数的正实数："^[0-9]+(.[0-9]{2})?$"。</span>
<span style="color: #0000ff;">　　只能输入有1~3位小数的正实数："^[0-9]+(.[0-9]{1,3})?$"。</span>
<span style="color: #0000ff;">　　只能输入非零的正整数："^\+?[1-9][0-9]*$"。</span>
<span style="color: #0000ff;">　　只能输入非零的负整数："^\-[1-9][]0-9"*$。</span>
<span style="color: #0000ff;">　　只能输入长度为3的字符："^.{3}$"。</span>
<span style="color: #0000ff;">　　只能输入由26个英文字母组成的字符串："^[A-Za-z]+$"。</span>
<span style="color: #0000ff;">　　只能输入由26个大写英文字母组成的字符串："^[A-Z]+$"。</span>
<span style="color: #0000ff;">　　只能输入由26个小写英文字母组成的字符串："^[a-z]+$"。</span>
<span style="color: #0000ff;">　　只能输入由数字和26个英文字母组成的字符串："^[A-Za-z0-9]+$"。</span>
<span style="color: #0000ff;">　　只能输入由数字、26个英文字母或者下划线组成的字符串："^\w+$"。</span>
<span style="color: #0000ff;">　　验证用户密码："^[a-zA-Z]\w{5,17}$"正确格式为：以字母开头，长度在6~18之间，只能包含字符、数字和下划线。</span>
<span style="color: #0000ff;">　　验证是否含有^%&amp;'',;=?$\"等字符："[^%&amp;'',;=?$\x22]+"。</span>
<span style="color: #0000ff;">　　只能输入汉字："^[\u4e00-\u9fa5]{0,}$"</span>
<span style="color: #0000ff;">　　验证Email地址："^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$"。</span>
<span style="color: #0000ff;">　　验证InternetURL："^http://([\w-]+\.)+[\w-]+(/[\w-./?%&amp;=]*)?$"。</span>
<span style="color: #0000ff;">　　验证电话号码："^(\(\d{3,4}-)|\d{3.4}-)?\d{7,8}$"正确格式为："XXX-XXXXXXX"、"XXXX- XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"和"XXXXXXXX"。</span>
<span style="color: #0000ff;">　　验证身份证号（15位或18位数字）："^\d{15}|\d{18}$"。</span>
<span style="color: #0000ff;">　　验证一年的12个月："^(0?[1-9]|1[0-2])$"正确格式为："01"～"09"和"1"～"12"。</span>
<span style="color: #0000ff;">　　验证一个月的31天："^((0?[1-9])|((1|2)[0-9])|30|31)$"正确格式为；"01"～"09"和"1"～"31"。整数或者小数：^[0-9]+\.{0,1}[0-9]{0,2}$</span>

</div>