---
title: JS观察者模式
date: 2020-11-03 15:55:05
tags:
---
- 设计模式

通常说对于事件的监听并触发回调函数就是观察者模式

``` js

// 引入 events 模块
const EventEmitter = require('events');
// 创建 eventEmitter 对象,这就是可观察对象
const myEmitter = new EventEmitter();

//观察者
function obs(...args){
    console.log(args.join(','));
}

// 订阅
myEmitter.on('event',obs);

// 发布
setTimeout(function() { 
myEmitter.emit('event',1,2,3)
}, 1000); 

//取消发布
myEmitter.removeListener('event',obs);

```