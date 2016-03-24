---
layout: post
title: CentOs搭建Php开发环境
category: project
description: CentOs搭建Apache、Mysql、Php开发环境
---

## 写在前面

今天需要搭建一个WikiMedia，然后找到了一个目前觉得最高大上的Web FTP，叫[KODExplorer][],是一个在线版的文件管理器，官方说法支持在线解压缩，支持在线预览，在线编辑等等。

## 动手搭建

### 安装Apache

使用 `yum` 进行安装

    sudo yum install httpd httpd-devel 

安装完成之后，启动`apache`服务器

    sudo systemctl start httpd.service

除了上面这条命令可以启动`apache`服务器之外，还可以使用`sudo service httpd start`,也会重定向到`httpd.service`.

启动之后，可以使用浏览器访问服务器的`IP`地址，`apache`默认端口为`80`，所以直接`http://$IP$`就可以了

### 安装Mysql

CentOs 7 系统yum源不自带`server`端的`mysql`，所以需要自己从官方下载[Repository][],进入页面之后选择自己相应的版本.我的服务器是`CentOs 7`,如果和我的机器相同，也可以直接使用下面的命令进行下载安装

    wget http://repo.mysql.com//mysql57-community-release-el7-7.noarch.rpm
    sudo rpm -ivh mysql57-community-release-el7-7.noarch.rpm
    sudo yum install mysql-community-server mysql mysql-devel

启动`mysql`

    sudo systemctl start mysqld.service

这时候如果你尝试登录`mysql -uroot -p`的话，会发现无法登录，而且你也不知道密码，因为`mysql`默认是没有密码的，这时候需要强制修改密码

首先，关闭`mysql`

    sudo service mysqld stop

刚才说过，和`systemctl`命令相同，用哪一种方式都行.然后打开`my.cnf`
    
    sudo vi /etc/my.cnf

在文件中最后一行添加`skip-grant-tables`,然后`:wq`保存并退出.之后重启`mysql`
    
    sudo service mysqld restart

强制登录`mysql`

    mysql

登录之后，分别执行以下命令，修改数据库登录密码为`123456`
    
    use mysql;
    update user set authentication_string=password('123456') where user='root';
    exit;

退出之后，再次编辑`my.cnf`,删除刚才添加的最后一行`skip-grant-tables`,保存退出，之后重新启动`mysql`

    sudo service mysqld restart

再次登录,输入密码`123456`就可以正常登录了

    msyql -uroot -p

### 安装Php

使用`yum`源进行安装
     
     sudo yum install php php-devel

重新启动`apache`，使得`php`生效

    sudo systemctl restart httpd.service

进入`\var\www\html`，新建一个测试文件
    
    cd \var\www\html
    sudo vi test.php

文件内容如下：

    <?php phpinfo(); ?>

通过浏览器访问该文件，会得到php版本信息，说明安装成功

### 安装php扩展

使用`yum`源进行安装

    sudo yum install php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring

重新启动`apache`，使得安装生效

    sudo systemctl restart httpd.service

[KODExplorer]: http://www.kalcaddle.com/
[Repository]: http://dev.mysql.com/downloads/repo/yum/