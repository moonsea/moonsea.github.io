---
layout:     post
title:      Amazing Programming
category: opinion
description: 简洁、高效、奇葩、难懂、好玩的Code
---

## 一句话交换变量a,b

### C

    a = (a+b) - (b=a)

### PHP

    list($a,$b) = array($b,$a);

## Python list交并补

### 交

    # method 1 #
    print list(set(a).intersection(set(b)))  # a & b

    # method 2 #
    val = [val for val in a if val in b]


### 并

    print list(set(a).union(set(b)))  # a + b

### 补

    print list(set(b).difference(set(a))) # b-a

## 提交参数输出Flag

### index.php
```
<?php

    $str = intval($_GET['id']);
    $reg = preg_match('/\d/is', $_GET['id']);
    if(!is_numeric($_GET['id']) and $reg !== 1 and $str === 1){
    	echo 'Flag';
    }else{
    	echo "Img";
    }

?>
```

### Solution

虽然PHP手册中介绍`intval()`不能对`array`和`object`进行整数化，但是通过实践发现`intval()`可以将非空数组转成`1`，将空数组转成`0`

```
index.php?id[]=a
```

## Python精确除法

总是会返回真实的商，不管操作数是整形还是浮点型

```
>>>from __future__ import division
>>>1/2
0.5
>>>1.0/2.0
0.5
```
