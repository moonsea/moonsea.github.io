---
layout: post
title: Github Disclosure
category: opinion
description: Records of a tool to detect github
---

## Github API

### Repo Url

> https://api.github.com/repos/moonsea/ecshop

---

- `name`: `ecshop`
- `owner`:
  - `login`: `moonsea`
  - `repos_url`: `https://api.github.com/users/moonsea/repos`
  - `followers_url`: `https://api.github.com/users/moonsea/followers`
  - `following_url`: `https://api.github.com/users/moonsea/following{/other_user}`
  - `starred_url`: `https://api.github.com/users/moonsea/starred{/owner}{/repo}`
  - `subscriptions_url`: `https://api.github.com/users/moonsea/subscriptions`
- `description`: `Ecshop`
- `branches_url`: `https://api.github.com/repos/moonsea/ecshop/branches{/branch}`
- `contents_url`: `https://api.github.com/repos/moonsea/ecshop/contents/{+path}`
- `languages_url`: `https://api.github.com/repos/moonsea/ecshop/languages`
- `merges_url`: `https://api.github.com/repos/moonsea/ecshop/merges`
- `git_url`: `git://github.com/moonsea/ecshop.git`
- `clone_url`: `https://github.com/moonsea/ecshop.git`
- `stargazers_count`: `1`
- `watchers_count`: `1`
- `language`: `PHP`
- `default_branch`: `master`

### Repo Content List

> https://api.github.com/repos/moonsea/ecshop/contents

---

* `name`: `activity.php`
* `path`: `activity.php`
* `size`: `4902`
* `url`: `https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master`
* `html_url`: `https://github.com/moonsea/ecshop/blob/master/activity.php`
* `download_url`: `https://raw.githubusercontent.com/moonsea/ecshop/master/activity.php`
* `type`: `file`/`dir`

### Repo Content Page

> https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master

---

* `name`: `activity.php`
* `path`: `activity.php`
* `size`: 4902
* `url`: `https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master`
* `html_url`: `https://github.com/moonsea/ecshop/blob/master/activity.php`
* `download_url`: `https://raw.githubusercontent.com/moonsea/ecshop/master/activity.php`
* `type`: `file`
* `content`:`lallaa`(base64解码)
* `encoding`: `base64`

## Python Grammar

### Base64编码解码

    import base64

    s = 'test'
    e = base64.b64encode(s) # 编码
    print e
    d = base64.b64decode(e) # 解码
    print d

### Json解析

    import json

    s = "{'a':'b','c':'d'}"
    j = json.loads(s)
    print j['a']

### API访问

POST请求

    import urllib2,urllib

    base_url = "https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master"

    # data={'status':'read','rating':3,'tag':'小说'}  # 根据api文档设置参数

    # data = urllib.urlencode(data) # 把参数进行编码

    # url = urllib2.Reuest(base_url,data) # 带参数访问

    url = urllib2.Request(base_url)

    response = urllib2.urlopen(url)

    content = response.read() # 返回json格式的字符串，类型string

    print content

Get请求

    import urllib2,urllib

    base_url = "https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master"

    # data={'status':'read','rating':3,'tag':'小说'}  # 根据api文档设置参数

    # data = urllib.urlencode(data) # 把参数进行编码

    # url = base_url + '?' + data # 带参数访问

    url = base_url

    response = urllib2.urlopen(url)

    content = response.read() # 返回json格式的字符串，类型string

    print content

### 创建文件夹/目录

    import os

    # 创建单级目录
    os.mkdir('/home/moonsea/codes/test') # 在 `/home/moonsea/codes`目录下，创建`test`文件夹,`/home/moonsea/codes`目录已经存在

    #  创建多级目录
    os.makedirs('/home/moonsea/codes/test') # 在`/home/moonsea`目录下，创建`codes`文件夹，并在`codes`文件夹下创建`test`文件夹，`/home/moonsea`目录已经存在，`codes`和`test`文件夹不存在

### 从Github下载文件

    使用`GitPython`进行下载

安装

    pip install gitpython #安装

使用

    from git import Repo

    clone_path = "/home/moonsea/codes/test"

    clone_url = "https://github.com/moonsea/ecshop.git"

    repo = Repo.clone_from(clone_url, clone_path, branch='master')

### 判断文件夹是否存在

    path = '/home/moonsea/codes/test'
    os.path.isdir(path) # 返回true/false

### 删除文件夹及内容

**shutil**

    import shutil

    path = '/home/moonsea/codes/test'
    shutil.rmtree(path)

在使用`shutil`的过程中，发现如果目录下存在**只读**文件，那么使用`rmtree`命令无法删除该文件，并且会提示`Access Denied`问题，解决方法可以增加异常处理机制，来接收抛出的错误，更改文件权限，然后进行删除，如下：

    shutil.rmtree(path,ignore_errors=False,onerror=errorRemoveReadonly)
    def errorRemoveReadonly(func, path, exc):
    excvalue = exc[1]
    if func in (os.rmdir, os.remove) and excvalue.errno == errno.EACCES:
        # change the file to be readable,writable,executable: 0777
        os.chmod(path, stat.S_IRWXU | stat.S_IRWXG | stat.S_IRWXO)  
        # retry
        func(path)
    else:
        raiseenter code here

另一种解决方法就是使用其他命令，来实现相同的功能，如调用系统命令那个，使用`rm -rvf /test`来删除文件夹及包含内容

    from subprocess import call
    call("rm -rf test/", shell=True)

### 遍历文件夹文件

    import os
    import os.path
    rootdir = “/home/sea/moonsea/test”                                   # 指明被遍历的文件夹

    for parent,dirnames,filenames in os.walk(rootdir):    #三个参数：分别返回1.父目录 2.所有文件夹名字（不含路径） 3.所有文件名字
     　　　for dirname in  dirnames:                       #输出文件夹信息
    　　　　  print "parent is:" + parent
    　　　　  print  "dirname is" + dirname

    　　　 for filename in filenames:                        #输出文件信息
    　　　　  print "parent is": + parent
    　　　　  print "filename is:" + filename
    　　     print "the full name of the file is:" + os.path.join(parent,filename) #输出文件路径信息

在使用`os.walk()`遍历文件夹时，可能需要忽略某个文件夹，或者某些文件夹，这种情况下，可以**重置遍历过程中的文件夹参数**来实现

    for parent,dirnames,filenames in os.walk(rootdir):
        dirnames[:] = []  # 重置文件夹，只输出根目录文件信息

需要注意的是，在重置文件夹时，需要使用`dirnames[:] = []`来重置，这样操作的是原变量，而使用`dir = []`操作并没有改变原有变量

## 常见系统配置

### PHP

详细内容参考[Github Disclosure -- PHP][]

## Acknowledgement

感谢老板给我机会做这个事情，希望能够把这件事情做好

## References

\[1\][用python实现接口测试][1]  
\[2\][Python中进行Base64编码和解码][2]  
\[3\][Python教程：\[61\]创建文件夹/目录][3]  
\[4\][raw_input() 与 input() __ Python][4]  
\[5\][shutil.rmtree fails on Windows with 'Access is denied'][5]  
\[6\][python 遍历文件夹 文件][6]  
\[7\][os.walk 忽略某个目录下的子目录][7]  
\[8\][十二个常见的PHP+MySql类免费CMS系统][8]  

[1]: http://www.tuicool.com/articles/fyayueV
[2]: http://blog.csdn.net/lxdcyh/article/details/4021476
[3]: http://jingyan.baidu.com/article/f3ad7d0ffe8b1409c2345b43.html
[4]: http://www.cnblogs.com/way_testlife/archive/2011/03/29/1999283.html
[5]: http://stackoverflow.com/questions/2656322/shutil-rmtree-fails-on-windows-with-access-is-denied
[6]: http://www.cnblogs.com/kaituorensheng/archive/2012/08/14/2638935.html
[7]: http://www.cnblogs.com/mozillazg/archive/2011/06/04/os-walk-ignore-subdirectories.html
[8]: http://m.jb51.net/article/16715.htm
[Github Disclosure -- PHP]: http://blog.moonsea.ac.cn/Github-PHP
