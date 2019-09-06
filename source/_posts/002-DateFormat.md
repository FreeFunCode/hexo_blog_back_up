---
title: C#/JavaScript/SqlServer 对日期时间的操作整理汇总
date: 2014-05-08 14:17:15
tags: 日期格式化
categories: CSharp后端
---

[点击查看原文](https://www.cnblogs.com/bugzone/p/Day20140508.html)
<div id="cnblogs_post_body" class="blogpost-body ">

&nbsp;&nbsp;&nbsp;&nbsp; <span style="text-decoration: line-through;">前言：针对C#/JavaScript/SqlServer常用的对日期时间的操作函数抽时间做了一个整理，网络上有许多，但是许多都不全，这些都是时间日期常用的一些操作！</span>

一、C# 常用日期时间操作

**//获取日期+时间**
DateTime.Now.ToString();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 2008-9-4 20:02:10
DateTime.Now.ToLocalTime().ToString();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 2008-9-4 20:12:12
DateTime.Now.ToString("yyyy-MM-dd&nbsp;HH:mm:ss")&nbsp;24小时制 
DateTime.Now.ToString("yyyy-MM-dd&nbsp;hh:mm:ss")&nbsp;&nbsp;12小时制
**//获取日期**
DateTime.Now.ToLongDateString().ToString();&nbsp;&nbsp;&nbsp; // 2008年9月4日
DateTime.Now.ToShortDateString().ToString();&nbsp;&nbsp;&nbsp; // 2008-9-4
DateTime.Now.ToString("yyyy-MM-dd");&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 2008-09-04
DateTime.Now.Date.ToString();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 2008-9-4 0:00:00
**//获取时间**
DateTime.Now.ToLongTimeString().ToString();&nbsp;&nbsp; // 20:16:16
DateTime.Now.ToShortTimeString().ToString();&nbsp;&nbsp; // 20:16
DateTime.Now.ToString("hh:mm:ss");&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 08:05:57
DateTime.Now.TimeOfDay.ToString();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; // 20:33:50.7187500
**//其他**
//n为一个数,可以数整数,也可以事小数
DateTime.Now.AddYears(n).ToString();&nbsp;&nbsp; //时间加n年
DateTime.Now.AddDays(n).ToString();&nbsp;&nbsp; //加n天
DateTime.Now.AddHours(n).ToString();&nbsp;&nbsp; //加n小时
DateTime.Now.AddMonths(n).ToString();&nbsp;&nbsp; //加n个月
DateTime.Now.AddSeconds(n).ToString();&nbsp;&nbsp; //加n秒
DateTime.Now.AddMinutes(n).ToString();&nbsp;&nbsp; //加n分

DateTime lastMonth = DateTime.Now.AddMonths(-1);
string LastMonth_firstDay = lastMonth.AddDays(1 - lastMonth.Day).ToString("yyyy-MM-dd");//获取上个月的第一天
string LastMonth_lastDay = lastMonth.AddDays(1 - lastMonth.Day).AddMonths(1).AddDays(-1).ToString("yyyy-MM-dd"); //获取上个月的最后一天

DateTime startWeek = dt.AddDays(1 - Convert.ToInt32(dt.DayOfWeek.ToString("d")));&nbsp; //本周周一
DateTime endWeek = startWeek.AddDays(6);&nbsp; //本周周日

&nbsp;DateTime startMonth = dt.AddDays(1 - dt.Day);&nbsp; //本月月初
DateTime endMonth = startMonth.AddMonths(1).AddDays(-1);&nbsp; //本月月末
//DateTime endMonth = startMonth.AddDays((dt.AddMonths(1) - dt).Days - 1);&nbsp; //本月月末

DateTime startQuarter = dt.AddMonths(0 - (dt.Month - 1) % 3).AddDays(1 - dt.Day);&nbsp; //本季度初
DateTime endQuarter = startQuarter.AddMonths(3).AddDays(-1);&nbsp; //本季度末

DateTime startYear = new DateTime(dt.Year, 1, 1);&nbsp; //本年年初
DateTime endYear = new DateTime(dt.Year, 12, 31);&nbsp; //本年年末

二、javascript 常用日期时间操作

&nbsp;&nbsp; var myDate = new Date(); //获取当前日期
&nbsp;&nbsp;&nbsp; myDate.getYear();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前年份(2位) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getFullYear();&nbsp;&nbsp; //获取完整的年份(4位,1970) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getMonth();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前月份(0-11,0代表1月) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getDate();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前日(1-31) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getDay();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前星期X(0-6,0代表星期天) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getTime();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前时间(从1970.1.1开始的毫秒数) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getHours();&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取当前小时数(0-23) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getMinutes();&nbsp;&nbsp;&nbsp; //获取当前分钟数(0-59) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getSeconds();&nbsp;&nbsp;&nbsp; //获取当前秒数(0-59) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.getMilliseconds();&nbsp;&nbsp; //获取当前毫秒数(0-999) &nbsp;
&nbsp;&nbsp;&nbsp; myDate.toLocaleDateString();&nbsp;&nbsp;&nbsp; //获取当前日期 &nbsp;
&nbsp;&nbsp;&nbsp; var mytime=myDate.toLocaleTimeString();&nbsp;&nbsp;&nbsp; //获取当前时间 &nbsp;
&nbsp;&nbsp;&nbsp; myDate.toLocaleString( );&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //获取日期与时间

三、SqlServer 日期时间操作

**1、获取当前时间** 

select getdate()

**2、截取需要的值**

select datepart(year,getdate())

select datepart(month,getdate())

select datepart(day,getdate())

select datepart(hour,getdate())

select datepart(minute,getdate())

select datepart(second,getdate())

select datepart(week,getdate())

**3、在日期中添加或减去指定的时间间隔**

select dateadd(year,3,getdate()) --获取当前时间，往后推迟三年

select dateadd(month,3,getdate()) --获取当前时间，往后推迟三个月

select dateadd(day,3,getdate()) --获取当前时间，往后推迟三天

select dateadd(hour,3,getdate()) --获取当前时间，往后推迟三小时

select dateadd(minute,3,getdate()) --获取当前时间，往后推迟三分钟

select dateadd(second,3,getdate()) --获取当前时间，往后推迟三秒钟

**4、返回两个日期之间的时间**

select datediff(year,'2001-08-19',getdate()) --2001-08-19和当前时间之间差多少年

select datediff(month,'2001-08-19',getdate()) --2001-08-19和当前时间之间差多少月

select datediff(day,'2001-08-19',getdate()) --2001-08-19和当前时间之间差多少天

**5、用不同的格式显示日期/时间**

select convert(char,getdate(),8)&nbsp; --显示当前时-分-秒

select convert(char,getdate(),10) --显示当前月-日-年，显示形式“08-19-11”

select convert(char,getdate(),11) --显示当前年-月-日，显示形式“11/08/19”

select convert(char,getdate(),14) --显示当前时-分-秒-毫秒，显示形式“14:54:57:090”

**6、其他**

SELECT CONVERT(datetime,CONVERT(char(8),GETDATE(),120)+'1')--这月的第一天 

select dateadd(d,-day(getdate()),dateadd(m,1,getdate()))--这月的最后一天&nbsp; &nbsp;

SELECT DATEADD(mm,DATEDIFF(mm,0,dateadd(month,-1,getdate())),0)--上月第一天 &nbsp;

select dateadd(ms,-3,DATEADD(mm,DATEDIFF(mm,0,getdate()),0))--上月最后一天
&nbsp;
select DATEADD(SS,-1,dateadd(day,1,CONVERT(varchar(15) , getdate(), 102 )))--获取当天的最后一刻

</div>