---
layout: post
title: Github Disclosure
category: opinion
description: Records of a tool to detect github
---

## Github API

### Repo Url

> https://api.github.com/repos/moonsea/ecshop

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
- `stargazers_count`: `1`
- `watchers_count`: `1`
- `language`: `PHP`
- `default_branch`: `master`

### Repo Content List

> https://api.github.com/repos/moonsea/ecshop/contents

* `name`: `activity.php`
* `path`: `activity.php`
* `size`: `4902`
* `url`: `https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master`
* `html_url`: `https://github.com/moonsea/ecshop/blob/master/activity.php`
* `download_url`: `https://raw.githubusercontent.com/moonsea/ecshop/master/activity.php`
* `type`: `file`/`dir`

### Repo Content Page

> https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master

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

## References

\[1\][用python实现接口测试][1]  
\[2\][ Python中进行Base64编码和解码][2]  


[1]: http://www.tuicool.com/articles/fyayueV
[2]: http://blog.csdn.net/lxdcyh/article/details/4021476
