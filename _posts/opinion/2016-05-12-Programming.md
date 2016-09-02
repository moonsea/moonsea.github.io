---
layout:     post
title:      Amazing Programming
category: opinion
description: 简洁、高效、奇葩、难懂的Code
---

## 一句话交换变量a,b

C

    a = (a+b) - (b=a)

PHP

    list($a,$b) = array($b,$a);

## Python list交并补

交

    print list(set(a).intersection(set(b)))  # a & b

并

    print list(set(a).union(set(b)))  # a + b

补

    print list(set(b).difference(set(a))) # b-a
