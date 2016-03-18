---
layout: post
title: CentOs 安装多线程下载工具
category: project
description: 在远程使用 VPS过程中，经常会遇到下载文件到远程服务器上的问题，比如克隆网站，下载百度云中的文件，下载BT，文中会一一提供方法.
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
![Fixcol2](/images/WabacusProblems/fixcol2.png)

## 单选1行

需求：鼠标点击表格中的记录，只能选择一行

方案：`<report rowselect="single>`

[Wabacus]: http://www.wabacus.org/
