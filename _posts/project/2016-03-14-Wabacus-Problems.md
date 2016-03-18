---
layout: post
title: Wabacus 开发问题汇总
category: project
description: 在使用 Wabacus 发过程中，遇到的各种各样的问题，进行记录以及解决.
---

## 写在前面

[Wabacus][]是目前我接触到的最方便进行进行办公系统，业务管理系统，适合多表单、表格的商务管理系统，同时还具备简单的图表展示，对数据处理、展示不是很复杂的系统也可以考虑使用。其基于Java 的 SSH 框架，在此基础上高度集成化，使用 XML 进行开发组织页面，开发人员可以在不熟悉或者会简单的 Java 编程，就可以迅速掌握并熟练使用的一个框架. 这个系统据学长说他和原作者保持着联系，不过我并没有见过.

这篇文章主要用来记录在开发过程中遇到的问题，以及相应的解决办法，所以本文不介绍如何使用 [Wabacus][] 系统，以及本地部署过程，具体的方法我会在另外一篇文章中介绍.其中很多问题可能仔细查看文档能够自己独立解决，因为现在正在使用 [Wabacus][] 进行开发，所以为了自己的方便，同时也方便和团队其他成员交流分享，所以大小问题都会记录下来，每个问题单独一个小节，有相似问题的可以直接跳到相关部分.

## 固定前2列

    <report id="epd601_report1" title="" type="editablelist2" 
    dataexport="richexcel" align="center" scrollwidth="1000px" width="1200px" 
    scrollheight="200px" fixedcols="2" fixedrows="title" rowselect="single">
    ...
    </report>

其中`fixedcols="2" fixedrows="title"`表示固定标签的前`2`列，同时固定每列的`title`，当然这两个标签不是必须同时使用的.效果如下：
![Fixcol2](/images/WabacusProblems/fixcol2.jpg)

## 单选1行

需求：鼠标点击表格中的记录，只能选择一行

方案：`<report rowselect="single>`

## 查询条件输入框中显示默认值

一般来说，输入框`inputbox`都会提供一个标签`defaultvalue`，当然[Wabacus][]也不例外，但是在查询条件中设置`defaultvalue`，却不会在页面中显示出来，而且在后台查询时也不会作为查询条件的默认值进入查询的`sql`语句，但是页面数据`<col></col>`中`<inputbox></inputbox>`使用`defaultvalue`能够在页面中显示出来，并作为其默认值。所以需要采取一种新的方法来实现查询条件输入框在页面中显示默认值.

需求：查询条件输入框在页面中显示当前日期  
方案：使用`doStart()`拦截器，格式化当前日期，并对页面查询条件输入框赋值

    public void doStart(ReportRequest rrequest, ReportBean rbean) {

        Format dateFormat = new SimpleDateFormat("yyyy/MM/dd");
        long currentTime = System.currentTimeMillis();
        Date da = new Date(currentTime);

        String date_s = rrequest.getStringAttribute("date_s");
        if(date_s == null || date_s.equals(""))
            rrequest.setAttribute("date_s", dateFormat.format(da));
        String date_e = rrequest.getStringAttribute("date_e");
        if(date_e == null || date_e.equals(""))
            rrequest.setAttribute("date_e", dateFormat.format(da));

    }

在本段代码中，

* `3-5`行：格式化当前日期
* `7-9`行：获取页面查询条件`date_s`输入框的值，如果有数据，则不添加默认值，没有数据，添加默认值.
* `10-12`行：获取页面查询条件`date_e`输入框的值，如果有数据，则不添加默认值，没有数据，添加默认值.
* `date_s`,`date_e`是页面查询条件输入框的`name`,例如：`<condition name="date_s" label="开始日期:" labelposition="left" right="30" br="true">`
* `doStart()`在每次查询后会重新加载，如果`7,10`不加入判断，那么用户输入无效，输入框的`value`一直是`9,12`所赋值

最终实现效果如下：
![conditiondefaultvalue](/images/WabacusProblems/conditiondefaultvalue.jpg)

## 固定列宽

需求：固定页面数据中每列显示数据的宽度  
方案：`<col column="actual_hours" label="实动工时" width="100px"></col>`

效果如下：
![colwidth](/images/WabacusProblems/colwidth.jpg)


## 致谢

感谢Adam的热心帮助

[Wabacus]: http://www.wabacus.org/