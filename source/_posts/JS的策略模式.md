---
title: JS的策略模式
date: 2020-11-02 16:43:36
tags:
---
- 设计模式

策略模式分为两部分，一部分是环境类，另一部分是策略类，环境类是接受命令的入口，策略类是无数策略的集合，通过策略模式将环境类与策略类连通，在环境的策略方法中可以自由替换策略

<!-- more -->

``` js

// 策略集合
var strategies = {
    'S':function (salary){
        return salary*4;
    },
    'A':function (salary){
        return salary*2;
    },
    'B':function (salary){
        return salary*6;
    },
    'D':function (salary){
        return salary*8;
    }
}

// 环境入口
var context = function (levle,salary){
    return strategies[levle](salary);
}

// 根据参数执行不同的环境策略
context('S',500);
context('B',500);
context('D',500);

```