---
layout: post
title: Ecshop 开发问题汇总
category: project
description: 在使用 Ecshop 开发过程中，遇到的各种各样的问题，进行记录以及解决.
---

## 写在前面

[Wabacus][]是目前我接触到的最方便进行进行办公系统，业务管理系统，适合多表单、表格的商务管理系统，同时还具备简单的图表展示，对数据处理、展示不是很复杂的系统也可以考虑使用。其基于Java 的 SSH 框架，在此基础上高度集成化，使用 XML 进行开发组织页面，开发人员可以在不熟悉或者会简单的 Java 编程，就可以迅速掌握并熟练使用的一个框架. 这个系统据学长说他和原作者保持着联系，不过我并没有见过.

这篇文章主要用来记录在开发过程中遇到的问题，以及相应的解决办法，所以本文不介绍如何使用 [Wabacus][] 系统，以及本地部署过程，具体的方法我会在另外一篇文章中介绍.其中很多问题可能仔细查看文档能够自己独立解决，因为现在正在使用 [Wabacus][] 进行开发，所以为了自己的方便，同时也方便和团队其他成员交流分享，所以大小问题都会记录下来，每个问题单独一个小节，有相似问题的可以直接跳到相关部分.

## 安装时提示：`gd_version()`

找到install/includes/lib_installer.php中的第31行`return ls_image::gd_version();`然后在找到include/cls_image.php中的678行，发现`gd_version()`方法未声明静态static，所以会出错

问题： 安装Ecshop时，出现错误提示信息`Strict Standards: Non-static method cls_image::gd_version() should not be called statically`
![Gd-Version](/images/ecshopproblems/16-04-05-1-gd-version.JPG)

方案： 下面两种方法选择一种即可

* 将function gd_version()改成static function gd_version()即可
* 或者将install/includes/lib_installer.php中的第31行return cls_image::gd_version();改成：

>    $p = new cls_image();
>    return $p->gd_version();

## JEPG显示不支持

查看发现有libjpeg.lib库，GD2库也有，都加载了，也都正常。查看ecshop源代码发现install/includes/lib_installer.php中第98行,JPEG写成了JPG

问题： 环境检测显示JEPG不支持
![jpeg-unsupport](/images/ecshopproblems/16-04-05-2-jepg-unsuport.JPG)

方案：将第98行代码改成如下形式

    $jpeg_enabled = ($gd_info['JPEG Support']        === true) ? $_LANG['support'] : $_LANG['not_support'];

## 错误警告`preg_replace()`

安装Ecshop完成之后，访问前台页面，会提示警告信息` Deprecated: preg_replace(): The /e modifier is deprecated`

问题： 错误警告` Deprecated: preg_replace(): The /e modifier is deprecated`
![Preg-Replace](/images/ecshopproblems/16-04-05-3-preg-replace.JPG)

方案： 打开`includes\cls_template.php`,跳转到第`300`行，将

    return preg_replace("/{([^\}\{\n]*)}/e", "\$this->select('\\1');", $source);
替换成

    return preg_replace_callback("/{([^\}\{\n]*)}/", function($r) { return $this->select($r[1]); }, $source);
重新访问页面就解决了

## 错误警告：`Only variables`

安装完成之后，访问前台首台，出现警告信息`Strict standards: Only variables should be passed by reference`

问题：错误警告`Strict standards: Only variables should be passed by reference`
![Only-Variable](/images/ecshopproblems/16-04-05-4-only-variable.JPG)

方案：PHP 5.3以上版本的问题，和配置有关.只要`includes\cls_template.php`第`418`行

    $tag_sel = array_shift(explode(' ', $tag));
拆成两句就没有问题了

    $tag_arr = explode(' ', $tag);
    $tag_sel = array_shift($tag_arr);

或者直接修改PHP配置文件`php.ini`第`451`行，将

    error_reporting = E_ALL
改成

    error_reporting = E_ALL
重新启动服务即可

## 参考资料

[1][Ecshop安装过程中的的问题：cls_image::gd_version()和不支持JPEG][1]  
[2][安装Ecshop首页出现报错：Only variables should be passed by referen][2]

[Wabacus]: http://www.wabacus.org/
[1]: http://www.cnblogs.com/thinksasa/archive/2013/03/12/2955922.html
[2]: http://www.cnblogs.com/wallis0922/archive/2012/12/08/2808199.html