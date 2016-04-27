---
layout: post
title: Ecshop 开发问题汇总
category: project
description: 在使用 Ecshop 开发过程中，遇到的各种各样的问题，进行记录以及解决.
---

## 写在前面

本文记录在Ecshop开发和使用过程中遇到的各种问题以及解决方法

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

## Ecshop后台`mktime()`错误警告

安装完成之后，登录系统后台访问“商店设置”时，系统报错，提示`mktime(): You should be using the time() function instead in`.这是因为PHP版本的问题，`mktime()`不带参数被调用时，会被提示错误.

问题：错误警告`mktime(): You should be using the time() function instead in`
![Mktime](/images/ecshopproblems/16-04-05-5-mktime.JPG)

方案：根据错误提示找到相应错误所在的文件，并且定位到问题代码，将

    $auth = mktime();

替换成

    $auth = time();

刷新页面就可以了

## 后台管理各页面功能备注

##### *htm*
---
`templates/index.htm`：后台主界面，通过`<frameset></frameset>`来实现页面分块  

##### *php*
---
`common.php`:后台基本显示语言  

##### *goods*表
---

`goods`: 添加`unit`字段作为商品单位  
`goods`: 添加`goods_bar_code`字段作为`商品条形码`  
`goods`: 添加`goods_max_buy`字段作为`限购数量`  
`goods`: 添加`goods_min_buy`字段作为`最低购买量`  
`goods`: 添加`goods_bind_type`字段作为`装订类型`  
`goods`: 添加`goods_composite_page`字段作为`合成图片格式`:0:单页,1:跨页  
`goods`: 添加`goods_composite_pic`字段作为`合成图片格式`:0:jpg,1:png  
`goods`: 添加`goods_gray`字段作为`灰度特效`:0:不启用,1:启用  
`goods`: 添加`goods_composite_pdf`字段作为`合并为pdf`:0:否,1:是  
`goods`: 添加`goods_album`字段作为`画册选项`:0:硬壳,1:软壳，2:皮革  
`goods`: 添加`goods_add_page_start`字段作为`起始页数`  
`goods`: 添加`goods_add_page_max`字段作为`最大页数`  
`goods`: 添加`goods_add_page_unit`字段作为`加页单位`  
`goods`: 添加`is_text_on`字段作为`开启文本`  
`goods`: 添加`is_pic`字段作为`开启用户图片`  
`goods`: 添加`is_background`字段作为`开启背景`  
`goods`: 添加`is_gif`字段作为`开启剪贴画`  
`goods`: 添加`is_border`字段作为`开启边框`  
`goods`: 添加`is_mask`字段作为`开启蒙版`  
`goods`: 添加`is_format`字段作为`开启版式`  
`goods`: 添加`is_textvr`字段作为`开启竖排文字`  
`goods`: 添加`goods_line_min`字段作为`最小行距`  
`goods`: 添加`goods_line_max`字段作为`最大行距`  
`goods`: 添加`goods_line_up_unit`字段作为`行距递增单位`  
`goods`: 添加`goods_defination`字段作为`分辨率`  
`goods`: 添加`goods_inner_width`字段作为`内页设计宽度`  
`goods`: 添加`goods_inner_height`字段作为`内页设计高度`  
`goods`: 添加`goods_blood_h`字段作为`普通页水平出血`  
`goods`: 添加`goods_blood_v`字段作为`普通页垂直出血`  
`goods`: 添加`goods_safe_line`字段作为`安全线`  

##### *ecs_goods_bind_type*表
---

新建`ecs_goods_bind_type`表`装订类型表`

>type_id:类型id  
>type_name：类型名称

##### *ecs_material_type*表
---

新建`ecs_material_type`表`材质类型表`

>type_id:类型id  
>type_name：类型名称  
>remark：备注

##### *admin_user*表
---


`admin_user`: 添加`invitation_code`字段作为`邀请码`  
`admin_user`: 添加`phone`字段作为`手机号码`  
`admin_user`: 添加`company`字段作为`所属部门`  
`admin_user`: 添加`bank_card`字段作为`银行卡号`  

##### *order_info*表
---

`order_info`:添加`invitation_code`字段作为`邀请码`  
`order_info`:`order_status`字段作为`印刷厂是否处理`:`0`:未确认，`1`：已确认  
`order_info`:添加`print_status`字段作为`印刷厂订单是否打印`:`0`:未打印订单，`1`：已打印订单  
`order_info`:添加`download_status`字段作为`印刷厂订单是否下载`:`0`:未下载订单，`1`：已下载订单  

##### *order_goods*表
---

`order_goods`:添加`bind_type`字段作为`装订类型`  
`order_goods`:添加`material_type`字段作为`材质`  
`order_goods`:添加`goods_height`字段作为`尺寸-长`  
`order_goods`:添加`goods_width`字段作为`尺寸-宽`  
`order_goods`:添加`goods_page_count`字段作为`商品页数`  
`order_goods`:添加`goods_url`字段作为`电子稿下载地址`  

## 时间戳数据库处理

    /* 添加按照付款时间每日分组 */
    $group = " GROUP BY FROM_UNIXTIME(oi.pay_time,'%y-%m-%d')";
    
    /* 查询距添加代理用户第几天 */
    $sql = "SELECT TO_DAYS(FROM_UNIXTIME(oi.pay_time)) - TO_DAYS(FROM_UNIXTIME(au.add_time)) AS day_num, ";
    
    /* 查询每日订单数 */
    $sql = $sql . " COUNT(oi.order_id) AS order_sum_day, ";
    
    /* 查询每日产品数 */
    $sql = $sql . " COUNT(og.goods_id) AS product_sum_day ";

## 参考资料

[1][Ecshop安装过程中的的问题：cls_image::gd_version()和不支持JPEG][1]  
[2][安装Ecshop首页出现报错：Only variables should be passed by referen][2]

[Wabacus]: http://www.wabacus.org/
[1]: http://www.cnblogs.com/thinksasa/archive/2013/03/12/2955922.html
[2]: http://www.cnblogs.com/wallis0922/archive/2012/12/08/2808199.html