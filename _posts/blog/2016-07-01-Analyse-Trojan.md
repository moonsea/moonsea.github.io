---
layout:     post
title:      记一次木马的分析
category: blog
description: 记录对一个木马样本的分析过程
---

## 写在前面

昨天老板给我发了一个木马样本，让我感兴趣来分析一下，现记录一下分析的过程。


## 样本

该样本是常见的诈骗短信的形式发送的，短信中提供一个链接，用户点击进行下载，如下图
![trojan-1](/images/trojan/trojan-1.jpg)

**样本下载链接**

[资料.apk](http://yrehgsc.com/r8yv)

`MD5` cedf2f74fe05d21767e841e2ba773e7a  
`SHA1` 20ab7e9eb388619fe6b039a2943610033e716281  
`SHA256` d470b3cd870308be601984f11cc5cc869dd26906b5439d0c72d31dea4392efc8  
`ssdeep6144` dakPMwH0MBb9LRuEMJ/IgnlZiK8b8Vm/Y:wwXb9LRuSKk8oY  
`File size` 203.0 KB ( 207851 bytes )  
`File type` Android  
`Magic literal` Zip archive data, at least v2.0 to extract  
`TrID`	Android Package (92.9%)  
`ZIP` compressed archive (7.0%)  

## 域名分析

拿到链接首先对链接进行分析，收集网站注册者信息。虽然注册者不一定就是木马作者，但可以作为前期的信息搜集，结合后续木马信息进行分析。

* 首先，得到`yrehgsc.com`的一个summay信息

![image_1aml95khrl7g1mt92p5151ga3tm.png-23.1kB][1]
从summary信息中可以得到该域名的排名，DNS服务器地址，以及该域名的IP地址`104.202.173.48`

* 然后，得到`yrehgsc.com`的网络信息
![image_1aml9qf30h6ufcch23gocm5v13.png-37.3kB][2]

* 测试其邮件服务器
![image_1amlbbi231e27lt61ra65l61qke1t.png-26.7kB][3]
结果没有发现在服务器上设置的邮件服务器

* 继续对`yrehgsc.com`进行同站分析

![image_1amlac7i51vd0jbq2no1b0a136p1g.png-42.9kB][4]
从分析结果中，可以看出在同一台服务器`104.202.173.48`上，存在`8`个绑定的域名。本文主要对`yrehgsc.com`以及`android木马`进行分析，对同站其他域名不进行分析。

* Whois信息

```
Domain Name: yrehgsc.com
Registry Domain ID:
Registrar WHOIS Server: whois.55hl.com
Registrar URL: http://www.55hl.com
Updated Date: 2016-6-16 12:23:00
Creation Date: 2016-6-16 12:23:06
Registrar Registration Expiration Date: 2017-6-16 12:23:06
Registrar: JIANGSU BANGNING SCIENCE & TECHNOLOGY CO. LTD
Registrar IANA ID: 1469
Registrar Abuse Contact Email: 496306249@qq.com
Registrar Abuse Contact Phone: +86.025-86883426-1009
Reseller:
Domain Status: ok http://www.icann.org/epp#ok
Registry Registrant ID:
Registrant Name: Gan Yong Yi Gan Yong Yi
Registrant Organization: Gan Yong Yi
Registrant Street: Tan Tang Qu Shan Bei Xiang Bao He Cun Deng Xi Tun4 Hao
Registrant City: Gui Gang Shi
Registrant State/Province: Guangxi
Registrant Postal Code: 100000
Registrant Country: CN
Registrant Phone: +86.01087878878
Registrant Phone Ext:
Registrant Fax: +86.01087878878
Registrant Fax Ext:
Registrant Email: 496306249@qq.com
Registry Admin ID:
Admin Name: Gan Yong Yi Gan Yong Yi
Admin Organization: Gan Yong Yi
Admin Street: Tan Tang Qu Shan Bei Xiang Bao He Cun Deng Xi Tun4 Hao
Admin City: Gui Gang Shi
Admin State/Province: Guangxi
Admin Postal Code: 100000
Admin Country: CN
Admin Phone: +86.01087878878
Admin Phone Ext:
Admin Fax: +86.01087878878
Admin Fax Ext:
Admin Email: removed email address
Registry Tech ID:
Tech Name: Gan Yong Yi Gan Yong Yi
Tech Organization: Gan Yong Yi
Tech Street: Tan Tang Qu Shan Bei Xiang Bao He Cun Deng Xi Tun4 Hao
Tech City: Gui Gang Shi
Tech State/Province: Guangxi
Tech Postal Code: 100000
Tech Country: CN
Tech Phone: +86.01087878878
Tech Phone Ext:
Tech Fax: +86.01087878878
Tech Fax Ext:
Tech Email: removed email address
Name Server: F1G1NS1.DNSPOD.NET
Name Server: F1G1NS2.DNSPOD.NET
DNSSEC: Unsigned
URL of the ICANN WHOIS Data Problem Reporting System: http://wdprs.internic.net/
>>> Last update of WHOIS database: 2016-7-1 8:57:23<<<
For more information on Whois status codes, please visit: visit: https://www.icann.org/resources/pages/epp-status-codes-list-2014-06-18-en

Billing ID:
Billing Name: Gan Yong Yi Gan Yong Yi
Billing Organization: Gan Yong Yi
Billing Street: Tan Tang Qu Shan Bei Xiang Bao He Cun Deng Xi Tun4 Hao
Billing City: Gui Gang Shi
Billing State/Province: Guangxi
Billing Postal Code: 100000
Billing Country: CN
Billing Phone: +86.01087878878
Billing Phone Ext:
Billing Fax: +86.01087878878
Billing Fax Ext:
Billing Email: 496306249@qq.com
```

从以上信息中，可以得到注册为` Gan Yong Yi `，邮箱为`496306249@qq.com`，具体地址为`广西省贵港市覃塘区山北乡保和村邓西屯4号`

* Whois反查
![image_1amlcm7flgqjjc2164v10qhhj22a.png-161.5kB][5]
通过对注册邮箱`496306249@qq.com`进行Whois信息反查，可以看到该邮箱注册有`19`个域名，而且域名都很奇怪。

* PS

查询结果如下

\[1\] [Whois信息查询][6]  
\[2\] [Whois信息发叉][7]


## 模拟运行

安装后点击运行，提示激活设备管理器，请求锁定屏幕权限
![image_1aml814kthgp1c6g5er1jmep639.png-271.7kB][8]

* 接下来不论点击`取消`或者`激活`，都会退出请求界面，并会提示`设备检测成功，可以放心使用`，再次点击无响应，且近期任务栏中也无此应用。重启后应用列表中无此应用。

## 在线扫描

在模拟器中模拟运行的结果，只能说明该应用存在问题，但是该应用是否是一款木马还需要进一步分析，首先采用线上平台进行扫描。

* **Virustotal**

![image_1amo9b93daibivr1hkm6b1taq5v.png-22.1kB][9]

通过在线杀软的检测，51款主流杀软中有27款杀软都能进行检测。

![image_1amo9efe2h1318jl81u152nf2h6c.png-81.2kB][10]

其中包括国内常用的`Qihoo-360`和`Tencent`, 刘超所在的公司和`KingSoft`以及`Microsoft`不能检测出问题。

* **Malware**

![image_1amlnk46tnmgpcknv4ajkm9s9.png-135kB][11]

因为Malware平台不支持Android平台应用的分析，所以最后的检测结果没有参考价值。

* **TCA**

该平台是由可信实验室苏璞睿老师团队研发的一个对外测试版本。
通过该平台共进行了四次检测，得到２种检测结果。

> 第１种检测结果

![image_1amlnspli6ql1ao71drpfbk1ud1m.png-51.3kB][12]

检测为高风险文件，共１处风险

![image_1amlnvlkqf6d1pdp1tqf1iv46b713.png-25.1kB][13]

报告显示为自动发送短信风险，并且能够自动获取手机IMEI,且有一张手机号码为`13886987589`的运行截图，归属地为`湖北江汉(天门-仙桃-潜江)`

![image_1amlo54n73p4ramqrsnq31btm1g.png-9.6kB][14]

> 第２种检测结果

![image_1amlodv6g16o11eei811cmq1inj1t.png-47.2kB][15]

检测为高风险文件，共２处风险

![image_1amloga3v1n17c9f1gd54f07e52a.png-31.8kB][16]

包括自动发送短信和隐藏服务商短信两个高危风险，以及一个自动获取手机IMEI信息得中危风险。运行过程种，并未出现异常截图。

![image_1amlok2bk19qsubphb81kgm18tg2n.png-65.8kB][17]

* **VisualThread**

![image_1amlq2l3i2tpot71o87ui1mu234.png-65kB][18]

![image_1amlq3n2jlgo1tbk1fubh4d8m33h.png-105.6kB][19]

> 检测结果如下

\[1\] [VirusTotal在线检测结果][20]
\[2\] [VisualThreat在线检测结果][21]

## 反编译分析

对`AndroidManifest.xml`进行分析

> 权限申请

![image_1amnm8l0e1a921lsbmte9uq19b79.png-126.5kB][22]

可以看到，该款木马申请了`读取联系人`,`访问网络`,`读取短信`,`写短信`,`发送短信`和`接收短信`等敏感权限。因为现在很多app开发过程中都存在权限冗余申请的问题，所以单纯总申请权限这方面还不足以来判定这是一款木马。

PS:

有意思的是这款app包名是`com.qihoo360.contacts`，即`360通讯录`.估计是想用360的名号来隐藏自己。

> 接收器

![image_1amnnla2d1h9mhn618rb1d0l1u67m.png-163.6kB][23]

`intent android:priority`取值通常设置为`-1000~1000`，数值越高，优先级越高可以看出，此处app将接收器(receiver)为了获取最高的优先级，将优先级声明为`2147483647`。
在此处，此款app以最高优先级获取了系统的状态以及短信，同时还申请了`BIND_DEVICE_ADMIN`，来获取设备管理的权限。

> 活动和服务

![image_1amno4aj51ev01a9k12791l00gi113.png-96kB][24]

此款app还注册了两个活动(activity)和3个服务(service)，实现开机自启动以及短信接收远程指令，接下来进行分析。

对app进行反编译，得到源码，但是app进行了混淆，源码并不容易阅读

> com.phone2.stop.activity.MainActivity

这是app启动的主函数入口。

![image_1amo4qqq8b7blh71bogl1b1nfm1g.png-25.3kB][25]

在程序开始运行时，会获取手机IMEI编号。

![image_1amo5n0a21uj13f1fo313s31nj32a.png-18.4kB][26]

并提示用户激活。

![image_1amo5ksb31hu01rcb13hheh3cdt1t.png-38.7kB][27]

用户激活之后自动进行卸载，并创建服务。

> SecondService

![image_1amo63iuf1v7ju8lbugk8516ed2n.png-14.9kB][28]

判断`BootService`是否启动，如未启动，则启动`BootService`。

> BootService

![image_1amo6dd66kqt1dnu1ddh1ng8ccg34.png-38.5kB][29]

启动时，自动获取系统`alarm`服务，并启动`SecondService`。

![image_1amo6hi9g1pj813lhm171ipi9fu3h.png-15.3kB][30]

同时，注册系统的`SMSReceiver`，用于接收远程短信指令。

> SmsService

![image_1amo6vieg6dtkgc3ni1de14qu3u.png-46.8kB][31]

获取远程短信指令

> 获取手机通讯录

![image_1amo82gb21lfp1ppb1nqqg3r12ni4b.png-53.9kB][32]

根据短信指令，获取手机通讯录，同时对通讯录进行过滤分析，如果通讯录中含有`张三`、`悟空`、`Billy1`联系人，视为测试环境，则不上传通讯录，否则将通讯录发送到指定服务器`mail.jidhcn.com`。通过对`mail.jidhcn.com`域名进行分析，与`yrehgsc.com`Whois信息一致，可以认定`Gan Yong Yi`即为木马作者。

![image_1amob6pml7q43vb4i517ggr996p.png-94kB][33]

> 获取手机短信

![image_1amo8dv7o1e2g23r1ai63ivc474o.png-68.4kB][34]

> 获取手机系统信息

![image_1amo8ik6jufh5vedll1b5a13eq55.png-24.6kB][35]

> 发送银行转账短信

![image_1amo8lh781ord115fh41t6e7pi5i.png-46.5kB][36]


## 总结

以上是对该安卓木马样本的分析。

通过分析，可以看出改款木马样本能够开机自启动，自删除，检测运行环境，通过注册系统服务，后台根据远程短信指令，获取手机联系人、短信，发送银行转账信息等，危害性较强。但从木马传播途径进行分析，该样本隐蔽性并不强，如果用户稍加注意，可以避免该款app的安装。同时，通过对app进行在线杀软的检测，360、Tencent等都能够进行检测，所以手机装有杀软也是有一定的必要的。

## References

\[1\] [AndroidManifest.xml详解（上）][37]  
\[2\] [Android属性之excludeFromRecents][38]


  [1]: http://static.zybuluo.com/moonsea/t0knb6bwfmhk3w2z8ibu4plu/image_1aml95khrl7g1mt92p5151ga3tm.png
  [2]: http://static.zybuluo.com/moonsea/qysovjs9ibjl8gjuy7da3ybc/image_1aml9qf30h6ufcch23gocm5v13.png
  [3]: http://static.zybuluo.com/moonsea/zc6t9kpitystjqts1ojx0gmc/image_1amlbbi231e27lt61ra65l61qke1t.png
  [4]: http://static.zybuluo.com/moonsea/bg6k0h6r2lw6z6jh6rhe0h10/image_1amlac7i51vd0jbq2no1b0a136p1g.png
  [5]: http://static.zybuluo.com/moonsea/744011gvg4uxmk9580xxolim/image_1amlcm7flgqjjc2164v10qhhj22a.png
  [6]: http://www.tcpiputils.com/browse/domain/eftgx.com
  [7]: http://whois.chinaz.com/reverse?host=496306249@qq.com&ddlSearchMode=1
  [8]: http://static.zybuluo.com/moonsea/zswakoflfinwb85t2v69jth0/image_1aml814kthgp1c6g5er1jmep639.png
  [9]: http://static.zybuluo.com/moonsea/bvn5hq3sbhann3rtts9phuc5/image_1amo9b93daibivr1hkm6b1taq5v.png
  [10]: http://static.zybuluo.com/moonsea/sxzxfcpp0wdd8jujrru599n8/image_1amo9efe2h1318jl81u152nf2h6c.png
  [11]: http://static.zybuluo.com/moonsea/imnmqc9fmvf5bagbn2vkjtu9/image_1amlnk46tnmgpcknv4ajkm9s9.png
  [12]: http://static.zybuluo.com/moonsea/yoxmtwlwhfmfpvbzhsaaxi86/image_1amlnspli6ql1ao71drpfbk1ud1m.png
  [13]: http://static.zybuluo.com/moonsea/igkmy53mi93x9q22ajswuqi4/image_1amlnvlkqf6d1pdp1tqf1iv46b713.png
  [14]: http://static.zybuluo.com/moonsea/e57qmujxakrymg9baj7kj9o9/image_1amlo54n73p4ramqrsnq31btm1g.png
  [15]: http://static.zybuluo.com/moonsea/zdh55p7ajt8nku9ulfmaz59r/image_1amlodv6g16o11eei811cmq1inj1t.png
  [16]: http://static.zybuluo.com/moonsea/p3x06zur2h1y50g70pf4tyki/image_1amloga3v1n17c9f1gd54f07e52a.png
  [17]: http://static.zybuluo.com/moonsea/7gd53fhil961xjnzb17pbcq5/image_1amlok2bk19qsubphb81kgm18tg2n.png
  [18]: http://static.zybuluo.com/moonsea/wp2i3llcqz5wf4ortxdov48o/image_1amlq2l3i2tpot71o87ui1mu234.png
  [19]: http://static.zybuluo.com/moonsea/rb7u21zcux4f48hd9u00jdc8/image_1amlq3n2jlgo1tbk1fubh4d8m33h.png
  [20]: https://virustotal.com/en/file/d470b3cd870308be601984f11cc5cc869dd26906b5439d0c72d31dea4392efc8/analysis/
  [21]: http://www.visualthreat.com/md5/CEDF2F74FE05D21767E841E2BA773E7A
  [22]: http://static.zybuluo.com/moonsea/wmrwx30newlceaa6q7w12col/image_1amnm8l0e1a921lsbmte9uq19b79.png
  [23]: http://static.zybuluo.com/moonsea/2uns15yd75eiiplx6pg09h1e/image_1amnnla2d1h9mhn618rb1d0l1u67m.png
  [24]: http://static.zybuluo.com/moonsea/hxw9f4p8v0t67rir8yp2e43d/image_1amno4aj51ev01a9k12791l00gi113.png
  [25]: http://static.zybuluo.com/moonsea/iwbm8kdsfu6t1hp6mzfh7xw9/image_1amo4qqq8b7blh71bogl1b1nfm1g.png
  [26]: http://static.zybuluo.com/moonsea/0af4rxbpes24r70ipqpr9a3u/image_1amo5n0a21uj13f1fo313s31nj32a.png
  [27]: http://static.zybuluo.com/moonsea/oostx2w3zq87mr0j69rj7sni/image_1amo5ksb31hu01rcb13hheh3cdt1t.png
  [28]: http://static.zybuluo.com/moonsea/bbu0xhouw5xrl4t3pd071558/image_1amo63iuf1v7ju8lbugk8516ed2n.png
  [29]: http://static.zybuluo.com/moonsea/9k3wwu4i2qottvz7s67hznu8/image_1amo6dd66kqt1dnu1ddh1ng8ccg34.png
  [30]: http://static.zybuluo.com/moonsea/u8lif6ekc1c75rt3493m35sy/image_1amo6hi9g1pj813lhm171ipi9fu3h.png
  [31]: http://static.zybuluo.com/moonsea/an43acid97b7eexi8tx6yxg1/image_1amo6vieg6dtkgc3ni1de14qu3u.png
  [32]: http://static.zybuluo.com/moonsea/4fq8mh7jmd76w91v33pk4u7p/image_1amo82gb21lfp1ppb1nqqg3r12ni4b.png
  [33]: http://static.zybuluo.com/moonsea/l0ubx9yq0lkwps52zlmmyni8/image_1amob6pml7q43vb4i517ggr996p.png
  [34]: http://static.zybuluo.com/moonsea/62lqx94j4nwm94ys7nwz1gmq/image_1amo8dv7o1e2g23r1ai63ivc474o.png
  [35]: http://static.zybuluo.com/moonsea/qz5dara8et6ojr9dpyca9o3v/image_1amo8ik6jufh5vedll1b5a13eq55.png
  [36]: http://static.zybuluo.com/moonsea/wrlm369vbdhhshj1mppl2c32/image_1amo8lh781ord115fh41t6e7pi5i.png
  [37]: http://blog.csdn.net/hudashi/article/details/7749972
  [38]: http://blog.csdn.net/laurawan/article/details/41819679
