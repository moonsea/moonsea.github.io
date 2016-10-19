---
layout: post
title: Github Disclosure -- PHP
category: opinion
description: Connection config in PHP
---

## native

### pdo

连接是通过创建 PDO 基类的实例而建立的。不管使用哪种驱动程序，都是用 PDO 类名。构造函数接收用于指定数据库源（所谓的 DSN）以及可能还包括用户名和密码（如果有的话）的参数。

    <?php
        $dbh = new PDO(string $dsn [, string $username [, string $password [, array $driver_options ]]]);
    ?>

**dsn**

> 数据源名称或叫做 DSN，包含了请求连接到数据库的信息。通常，一个 DSN 由 PDO 驱动名、紧随其后的冒号、以及具体 PDO 驱动的连接语法组成。更深入的信息能从 PDO 具体驱动文档找到。

**username**

> DSN字符串中的用户名。对于某些PDO驱动，此参数为可选项。

**password**

> DSN字符串中的密码。对于某些PDO驱动，此参数为可选项。

**driver_options**

> 一个具体驱动的连接选项的键=>值数组。

PDO驱动接口列表

|*驱动名称*       | *支持的数据库*                                |
|----------------|-------------------------------------------|
|PDO_CUBRID      |Cubrid                                     |
|PDO_DBLIB       |FreeTDS / Microsoft SQL Server / Sybase    |
|PDO_FIREBIRD    | Firebird/Interbase 6                      |
|PDO_IBM         |IBM DB2                                    |
|PDO_INFORMIX    |IBM Informix Dynamic Server                |
|PDO_MYSQL       |MySQL 3.x/4.x/5.x                          |
|PDO_OCI         |Oracle Call Interface                      |
|PDO_ODBC        |ODBC v3 (IBM DB2, unixODBC and win32 ODBC) |
|PDO_PGSQL       |PostgreSQL                                 |
|PDO_SQLITE      |SQLite 3 及 SQLite 2                       |
|PDO_SQLSRV      |Microsoft SQL Server / SQL Azure           |
|PDO_4D          |4D                                         |


示例

    <?php
        $servername = "localhost";
        $username = "username";
        $password = "password";

        try {
            $conn = new PDO("mysql:host=$servername;dbname=myDB", $username, $password);
            echo "连接成功";
        }
        catch(PDOException $e)
        {
            echo $e->getMessage();
        }
    ?>

### mysqli

语法

    $conn = new mysqli($servername, $username, $password);

示例

    <?php
        $servername = "localhost";
        $username = "username";
        $password = "password";

        // 创建连接
        $conn = new mysqli($servername, $username, $password);

        // 检测连接
        if ($conn->connect_error) {
            die("连接失败: " . $conn->connect_error);
        }
        echo "连接成功";
    ?>

- mysql_connect

语法

    # 面向对象
    mysql_connect(servername,username,password);
    # 面向过程
    mysqli_connect($servername, $username, $password);

示例

    # 面向对象
    <?php
        $con = mysql_connect("localhost","peter","abc123");
        if (!$con)
        {
          die('Could not connect: ' . mysql_error());
        }
    ?>

    #面向过程
    <?php
        $servername = "localhost";
        $username = "username";
        $password = "password";

        // 创建连接
        $conn = mysqli_connect($servername, $username, $password);

        // 检测连接
        if (!$conn) {
            die("Connection failed: " . mysqli_connect_error());
        }
        echo "连接成功";
    ?>

## modify

### DEDECMS

一款国内开源的cms，作者是一个个人，能做出如此功能的cms，是相当不错的。2007版功能十分强大，希望能改善之前数据量一大，更新静态页就很慢的缺点。因为开源，有较多的玩家和拥护者。非常适合有一定编程基础的站长。

官方网站： dedecms.com

### phpcms

一个综合的网站管理系统，由PHP+MYSQL构架全站生成html，能够快速高效地应用于LINUX和WINDOWS服务器平台，是目前中国LINUX环境下最佳的网站管理应用解决方案之一。据传被酷6收购。

官方网站： phpcms.cn

### 帝国网站管理系统

Ecms全称为"帝国网站管理系统"，英文译为"Empire CMS"简称"Ecms".Ecms是基于B S结构，且功能强大而易用的网站管理系统.是一个经过完善设计的适用于Linux windows Unix等环境下高效的网站解决方案。

官方网站： phome.net

### php168

PHP168整站系统，代码全部开源，可方便的进行二次开发，功能模块可以自由安装与删除，个人用户免费使用。系统频道模块很多，适合作个人门户网站。较多页面没有生成静态页。如果你想建站，就义无反顾的选择它吧!!!

官方网站： php168.com

### HBcms

一个以PHP官方网站推荐的PEAR+SMARTY技术架构的cms，比较容易上手，适合没经验的新人做网站。没有下载，分类信息等模块，适合做文章为主的网站。全站生成静态页，默认附带了几套模板，可以方便的更换模板。个人企业都免费，无需授权。

官方网站： hbcms.com

### SupSite

一款将论坛资源自动转换成门户网站的php程序系统，使用SupeSite，并利用你现有的论坛，你将自动拥有一个功能完备的，资源丰富的站点系统;由论坛变成网站，一切都是自动完成，你不需要任何干涉。让你轻轻松松实现建立网站的目的。

官方网站： supsite.net

### 曼波

MAMBO，一个国外的CMS系统，功能很强大，支持添加很多组件，模块;拥有丰富的模板.Mambo是一个网站内容管理系统(CMS)，它是网站的后台引擎，使网站内容的创建、管理和共享更加简易。Mambo十分强大，但官方网站网站也承认，它不是典型的“门户”网站解决方案。

官方网站：mamboserver.com

### Joomla!

是一套在国外相当知名的内容管理系统，2007年开源cms第一名!Joomla!是使用PHP语言加上MySQL数据库所开发的软件系统，可以在Linux、Windows、MacOSX等各种不同的平台上执行。操作接口除了美观之外，也花了很多心力在设计这些接口的简易操作性。但初次使用者，需要花一点时间学习一下操作的方式，才能运用自如。。

官方网站： joomla.org

### Drupal

是一个强大的软件，它可以让个人或社区使用者很容易地发表、管理并组织一个网站里大量且多样的内容。已经有许多个人和组织采用Drupal来建立各种不同的网站。Drupal是一套采用GPL授权的开放源码软件，是由数以千计的使用者和开发人员所共同维护和开发的。

官方网站： drupal.org

### WordPress

是一款基于PHP和MySQL的Blog软件，但是它也可以当作简单的cms系统来用。通过它可以快速而简便的搭建属于你自己的Blog(网站)平台。简而言之,这个Wordpress就相当于咱们用来搭建论坛的那些程序,比如用在自留地上的雷傲,还有别的比如PHPBB等等…Wordpress因为它的安装简单和可扩展性好几乎已经成了独立搭建Blog平台的第一选择。Wordpress还有一个MU就是多用户的版本,支持多用户的Blog系统。

官方网站： wordpress.org

### Kesion

集安全、稳定、强大、易用于一体，主系统包括了文章，图片，下载，商场，供求，动漫，音乐，影视，求职招聘系统︱完美支持自定义模型︱自定义表单等功能。程序与模板分离模式，让你模板制作更方便，同时系统支持整站生成全静态格式与动态方便选择;支持文章采集，自定义模型的自定义字段的采集等功能;完全开源，您可以根据自己的想法设计网页从而建立一个具有自我的风格的网站，版本更新速度快，不断有新版本出现，还可以与第三方软件完美整合。

官方网站： kesion.com

### Powereasy

《动易网站管理系统》是当前中国最具性价比、最受欢迎的CMS系统和电子商务系统，目前已有超过30万个以上网站的应用规模，并拥有几千名商业用户，涉及到政府、企业、科研教育和媒体等各个行业领域。
您可能感兴趣的文章:
Thinkphp实现MySQL读写分离操作示例
使用PHP实现Mysql读写分离
PHP基于单例模式实现的mysql类
php封装的连接Mysql类及用法分析
一个php Mysql类 可以参考学习熟悉下
php实现mysql封装类示例
PHP访问MYSQL数据库封装类(附函数说明)
php实现带读写分离功能的MySQL类完整实例

### destoon      

Destoon B2B网站管理系统是一套完善的B2B(电子商务)行业门户解决方案

## References

\[1\][十二个常见的PHP+MySql类免费CMS系统][1]  

[1]: http://m.jb51.net/article/16715.htm
