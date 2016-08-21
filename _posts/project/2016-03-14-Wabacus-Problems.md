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

---
`2016-04-02`

## 开发环境搭建问题

### Stream Closed

在初始搭建Wabacus项目开发环境时，需要配置好本地环境`Tomcat 7`和`Java 1.7`,但是只有这些还是不够，因为在运行程序的时候，会发现系统报错，提示`Stream Closed`,具体原因不知道，可能是因为导入依赖包时，系统无法完全识别，所以需要重新导入.

问题：系统运行报错`Stram Closed`
方案：项目配置中重新导入资源依赖包

首先，选择项目点击右键，选择"打开模块设置"(Open Module Settings)
![Open-Module-Settings](/images/WabacusProblems/16-04-02-1-open-module-settings.JPG)

然后，选择“模块”(Modules)
![Module](/images/WabacusProblems/16-04-02-2-module.JPG)

在右侧区域选择“依赖”(Denpencies)
![Denpencies](/images/WabacusProblems/16-04-02-3-depencies.JPG)

最后把下面的依赖包全部删除，只留下Java本地资源包，然后重新导入系统资源依赖包.资源依赖包的路径是`/WebRoot/WEB-INF/lib`,把全部`jar`包重新导入就可以了.
![Add-Jars](/images/WabacusProblems/16-04-02-4-add-jars.JPG)

### ClassNotFoundException

在搭建环境的过程中，可能会遇到这样的问题：虽然已经在`Modules`的`Denpencies`中加入了依赖包，但是在启动服务器部署的时候，控制台还是会提示`java.lang.ClassNotFoundException: com.wabacus.WabacusServlet`。虽然还是不太清楚具体什么原因，依赖包没有添加进去，根据控制台报错信息，`See Servlet Spec 3.0, section 10.7.2`,查阅过相关资料[1]后，说是有重复的`lib`，但是在尝试删掉提示的`jar`包之后，问题依然存在，于是开始寻找其他解决方法。折腾了一下午，终于找到了解决方法，如下：

首先，依然是打开`Module Settings`，选择左侧的`Artifacts`
![ClassNotFound-1](/images/WabacusProblems/2016-08-21-ClassNotFound-1.jpg)

然后，添加一个`Web Application:Exploded`,并选择`From Modules...`
![ClassNotFound-2](/images/WabacusProblems/2016-08-21-ClassNotFound-2.jpg)

会弹出提示，选择要添加的`Module`，选择之后，点击`OK`就好了
![ClassNotFound-3](/images/WabacusProblems/2016-08-21-ClassNotFound-3.jpg))

自定义名称(`Name`)和`Output directory`,并勾选`Build on make`，之后进行**最关键**的一步,将右侧`Avaliable Elements`下的`lib`,点击右键，选择`Extract Into /WEB-INF/classes`,之后点击左侧的`WEB-INF`-`classes`就会看到`Extracted xxxxxxx`  (因为在中间操作过程中，不能截图，就只放上了最后的结果图)
![ClassNotFound-4](/images/WabacusProblems/2016-08-21-ClassNotFound-4.jpg)

最后，在`Tomcat`服务器中，配置`Deployment`,添加选择`Artifact`,配置相应的部署目录就可以了
![ClassNotFound-5](/images/WabacusProblems/2016-08-21-ClassNotFound-5.jpg)

![ClassNotFound-6](/images/WabacusProblems/2016-08-21-ClassNotFound-6.jpg)

启动`Tomcat`,就可以成功运行访问了
![ClassNotFound-7](/images/WabacusProblems/2016-08-21-ClassNotFound-7.jpg)

## 致谢

感谢Adam的热心帮助

[Wabacus]: http://www.wabacus.org/
[1]: http://blog.csdn.net/roxliu/article/details/20161011
