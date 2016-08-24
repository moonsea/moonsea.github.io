---
layout: post
title: Github Disclosure
category: opinion
description: Records of a tool to detect github
---

## Github API

> https://api.github.com/repos/moonsea/ecshop

* `name`: `ecshop`
* `owner`:
 - `login`
 - `repos_url`: `https://api.github.com/users/moonsea/repos`
 - `followers_url`: `https://api.github.com/users/moonsea/followers`
 - `following_url`: `https://api.github.com/users/moonsea/following{/other_user}`
 - `starred_url`: `https://api.github.com/users/moonsea/starred{/owner}{/repo}`
 - `subscriptions_url`: `https://api.github.com/users/moonsea/subscriptions`
* `description`: `Ecshop`
* `branches_url`: `https://api.github.com/repos/moonsea/ecshop/branches{/branch}`
* `contents_url`: `https://api.github.com/repos/moonsea/ecshop/contents/{+path}`
* `languages_url`: `https://api.github.com/repos/moonsea/ecshop/languages`
* `merges_url`: `https://api.github.com/repos/moonsea/ecshop/merges`
* `git_url`: `git://github.com/moonsea/ecshop.git`
* `stargazers_count`: `1`
* `watchers_count`: `1`
* `language`: `PHP`
* `default_branch`: `master`

> https://api.github.com/repos/moonsea/ecshop/contents

* `name`: `activity.php`
* `path`: `activity.php`
* `size`: `4902`
* `url`: `https://api.github.com/repos/moonsea/ecshop/contents/activity.php?ref=master`
* `html_url`: `https://github.com/moonsea/ecshop/blob/master/activity.php`
* `download_url`: `https://raw.githubusercontent.com/moonsea/ecshop/master/activity.php`
* `type`: `file`/`dir`

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
