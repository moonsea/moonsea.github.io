---
layout: post
title: Github Disclosure -- PHP
category: opinion
description: Connection config in PHP
---

## native

### pdo

---

连接是通过创建 PDO 基类的实例而建立的。不管使用哪种驱动程序，都是用 PDO 类名。构造函数接收用于指定数据库源（所谓的 DSN）以及可能还包括用户名和密码（如果有的话）的参数。

    <?php
        $dbh = new PDO(string $dsn [, string $username [, string $password [, array $driver_options ]]]);
    ?>

- dsn

> 数据源名称或叫做 DSN，包含了请求连接到数据库的信息。通常，一个 DSN 由 PDO 驱动名、紧随其后的冒号、以及具体 PDO 驱动的连接语法组成。更深入的信息能从 PDO 具体驱动文档找到。

- username

> DSN字符串中的用户名。对于某些PDO驱动，此参数为可选项。

- password

> DSN字符串中的密码。对于某些PDO驱动，此参数为可选项。

- driver_options

> 一个具体驱动的连接选项的键=>值数组。

#### **PDO驱动接口**

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

- [PDO_CUBRID][]

> PDO_CUBRID是PHP对CUBRID数据库进行访问的PHP数据对象接口驱动。目前PDO_CUBRID不支持继续进行连接。

示例

    <?php
        $conn_str ="cubrid:dbname=demodb;host=localhost;port=33000";
        $cubrid_pdo = new PDO($conn_str, 'dba', '');

        $cubrid_pdo->exec("DROP TABLE if exists test_tbl");
        $cubrid_pdo->exec("CREATE TABLE test_tbl (col_1 SET(VARCHAR))");

        $sql_stmt_insert = "INSERT INTO test_tbl VALUES (?);";
        $stmt = $cubrid_pdo->prepare($sql_stmt_insert);
        $data = array("abc","def","ghi");
        $ret = $stmt->bindParam(1, $data, PDO::PARAM_NULL);
        $ret = $stmt->execute();
        var_Dump($ret);
    ?>

- [PDO_DBLIB][]

> PDO_DBLIB是PHP对Microsoft SQL Server和Sybase数据库进行访问的PHP数据对象接口驱动,访问通过FreeTDS库来实现.该扩展目前在Windows平台上PHP 5.3及以后版本不再支持.

dsn前缀

    mssql:host=localhost;dbname=testdb
    sybase:host=localhost;dbname=testdb
    dblib:host=localhost;dbname=testdb

- [PDO_FIREBIRD][]

> PDO_FIREBIRD是PHP对Firebird数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    firebird:dbname=hostname/port:/path/to/DATABASE.FDB

示例

    $str_conn = "firebird:dbname=C:\db\banco.gdb;host=localhost";

    $dbh = new PDO($str_conn, "SYSDBA", "masterkey");

- [PDO_IBM][]

> PDO_IBM是PHP对IBM数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    ibm

dsn连接分为3种

- 配置文件
> db2cli.ini or odbc.ini

示例

    $db = new PDO("ibm:DSN=DB2_9", "", "");

    [DB2_9]
    Database=testdb
    Protocol=tcpip
    Hostname=11.22.33.444
    Servicename=56789

- 数据库登记名称
> db2客户端登记册中的数据库别名

- 完全连接字符串
> DRIVER={IBM DB2 ODBC DRIVER};DATABASE=database;HOSTNAME=hostname;
    PORT=port;PROTOCOL=TCPIP;UID=username;PWD=password;

示例

    $db = new PDO("ibm:DRIVER={IBM DB2 ODBC DRIVER};DATABASE=testdb;" .
      "HOSTNAME=11.22.33.444;PORT=56789;PROTOCOL=TCPIP;", "testuser", "tespass");

- [PDO_INFORMIX][]

> PDO_IBM是PHP对Informix数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    informix

dsn连接分为2种

- 配置文件
> odbc.ini

示例

    $db = new PDO("informix:DSN=Infdrv33", "", "");
    [ODBC Data Sources]
    Infdrv33=INFORMIX 3.3 32-BIT

    [Infdrv33]
    Driver=/opt/informix/csdk_2.81.UC1G2/lib/cli/iclis09b.so
    Description=INFORMIX 3.3 32-BIT
    Database=common_db
    LogonID=testuser
    pwd=testpass
    Servername=ids_server
    DB_LOCALE=en_US.819
    OPTIMIZEAUTOCOMMIT=1
    ENABLESCROLLABLECURSORS=1

- 完全连接字符串(complete connection string)

示例

    $db = new PDO("informix:host=host.domain.com; service=9800;
        database=common_db; server=ids_server; protocol=onsoctcp;
        EnableScrollableCursors=1", "testuser", "tespass");

- [PDO_MYSQL][]

> PDO_MYSQL是PHP对MYSQL 3.x,4.x,5.x数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    mysql

示例

    mysql:host=localhost;dbname=testdb
    mysql:host=localhost;port=3307;dbname=testdb
    mysql:unix_socket=/tmp/mysql.sock;dbname=testdb

- [PDO_OCI][]

> PDO_OCI是PHP对ORACLE数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    oci

dbname分为2种

- 直接连接Oracle数据库

> dbname=//hostname:port-number/database

    // Connect using the Oracle Instant Client
    oci:dbname=//localhost:1521/mydb

- `tnsnames.ora`中定义的数据库

> dbname=database  
> `tnsnames.ora` in `%ORACLE_HOME%\network\admin`

    // Connect to a database defined in tnsnames.ora
    oci:dbname=mydb

示例

    <?php
        $dbc = new PDO('oci:dbname=192.168.10.145/orcl;charset=CL8MSWIN1251', 'username', 'password');
    ?>

- [PDO_ODBC][]

> PDO_ODBC是PHP通过ODBC驱动或者IBM DB2调用层接口库来对数据库进行访问的PHP数据对象接口驱动.

PDO_ODBC分为3种类型

- ibm-db2
> 支持通过`free DB2`客户端访问`IBM DB2 Universal Database`,`Cloudscape`和`Apache Derby servers`

- unixODBC
> 支持通过`unixODBC`驱动管理器和数据库自带ODBC驱动来访问数据库

- generic
> 为ODBC驱动管理器提供编译选项，但是`PDO_ODBC`不明确支持

dsn前缀

    odbc

示例

- 连接注册ODBC驱动

>

    // connect to an ODBC database cataloged as testdb in the ODBC driver manager

    odbc:testdb

- 连接ibm-db2

>

    // connect to an IBM DB2 database named SAMPLE using the full ODBC DSN

    odbc:DRIVER={IBM DB2 ODBC DRIVER};HOSTNAME=localhost;PORT=50000;DATABASE=SAMPLE;PROTOCOL=TCPIP;UID=db2inst1;PWD=ibmdb2;

- 连接Microsoft Access

>

    // connect to a Microsoft Access database stored at C:\db.mdb using the full ODBC DSN

    odbc:Driver={Microsoft Access Driver (\*.mdb)};Dbq=C:\\db.mdb;Uid=Admin

- [PDO_PGSQL][]

> PDO_PGSQL是PHP通过对PostgreSQL数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    pgsql

示例

    <?php
        $dbh = new PDO(pgsql:host=localhost;port=5432;dbname=testdb;user=bruce;password=mypass);
    ?>

- [PDO_SQLITE][]

> PDO_SQLITE是PHP通过对SQLite 3数据库进行访问的PHP数据对象接口驱动.

dsn前缀

    # sqlite 3
    sqlite:

    # sqlite 2
    sqlite2:

两种类型

- 访问磁盘上的数据库

> 使用数据库在系统中的绝对路径

示例

    # sqlite 3
    sqlite:/opt/databases/mydb.sq3

    # sqlite 2
    sqlite2:/opt/databases/mydb.sq2
- 在内存中创建数据库

> `:memory:`

示例

    # sqlite 3
    sqlite::memory:

    # sqlite 2
    sqlite2::memory:

- [PDO_SQLSRV][]

> PDO_SQLSRV是PHP通过对MS SQL Server(server 2005及其以后版本)和SQL Azure数据库进行访问的PHP数据对象接口驱动.  
> PDO_SQLSRV扩展仅对Windows平台PHP兼容。Linux平台可以参考`[ODBC][]`和`[Microsoft's SQL Server ODBC Driver for Linux][]`

dsn前缀

    sqlsrv:

示例

- 连接特定MS SQL Server数据库

    $c = new PDO("sqlsrv:Server=localhost;Database=testdb", "UserName", "Password");

- 连接特定端口MS SQL Server

    $c = new PDO("sqlsrv:Server=localhost,1521;Database=testdb", "UserName", "Password");

- 连接SQL Azure数据库

    $c = new PDO("sqlsrv:Server=12345abcde.database.windows.net;Database=testdb", "UserName@12345abcde", "Password");

- [PDO_4D][]

> PDO_4D是PHP通过对4D数据库进行访问的PHP数据对象接口驱动.  
> PDO_4D是PHP实验性功能  

dsn前缀

    4D:

示例

    4D:host=localhost;charset=UTF-8

### mysqli

---

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
[PDO_CUBRID]: http://php.net/manual/zh/ref.pdo-cubrid.php
[PDO_DBLIB]: http://php.net/manual/zh/ref.pdo-dblib.php
[PDO_FIREBIRD]: http://php.net/manual/zh/ref.pdo-firebird.php
[PDO_IBM]: http://php.net/manual/zh/ref.pdo-ibm.php
[PDO_INFORMIX]: http://php.net/manual/zh/ref.pdo-informix.php
[PDO_MYSQL]: http://php.net/manual/zh/ref.pdo-mysql.php
[PDO_OCI]: http://php.net/manual/zh/ref.pdo-oci.php
[PDO_ODBC]: http://php.net/manual/zh/ref.pdo-odbc.php
[ODBC]: http://php.net/manual/zh/ref.pdo-odbc.php
[PDO_PGSQL]: http://php.net/manual/zh/ref.pdo-pgsql.php
[PDO_SQLITE]: http://php.net/manual/zh/ref.pdo-sqlite.php
[PDO_SQLSRV]: http://php.net/manual/zh/ref.pdo-sqlsrv.php
[Microsoft's SQL Server ODBC Driver for Linux]: https://www.microsoft.com/en-us/download/details.aspx?id=28160
[PDO_4D]: http://php.net/manual/zh/ref.pdo-4d.php
