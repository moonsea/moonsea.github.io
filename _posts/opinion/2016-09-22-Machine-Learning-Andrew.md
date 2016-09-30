
---
layout:     post
title:      Machine Learning of Andrew
category: opinion
description: Machine Leaning 学习笔记
---

## Supervised Learning

### Classification

predict distinct value

### Regression

predict continuous value

## Unsupervised Learning

Given the data set and do not told what to do with it and do not told what each data point is. Instead just told, here is a data set, can you find some structure in the data?

### [Cocktail party problem][]

Two people talking at the same time in a small room, where put two microphones. Because these two microphones are at two different positions with different distance from two speakers, each microphone records a different combination of these two speakers voices. Maybe speaker one is a little louder in microphone one and speaker two is a little louder in microphone two, but each microphone would cause an overlapping combination of both speakers' voices.

Cocktail party algorithm is used to separate these two records recorded by two microphones.
![cocktail party problem](/images/mlandrew/cocktail_party_problem.jpg)

## Model & Cost Function

$$ D+1 $$

```flow
op1=>operation: 手动导入导出
op2=>operation: 自动导入导出
op3=>operation: 自动分析
op4=>operation: 产出识别的僵尸网络的数据

op1(right)->op2
op2(right)->op3
op3(right)->op4
```


## 致谢

Thanks for Coursera

## References
\[1\]: [说说鸡尾酒会问题(Cocktail Party Problem)和程序实现][1]  

[Cocktail party problem]: https://www.coursera.org/learn/machine-learning/lecture/olRZo/unsupervised-learning
[1]: http://blog.csdn.net/mrharvey/article/details/18598605
