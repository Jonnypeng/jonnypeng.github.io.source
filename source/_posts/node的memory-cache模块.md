---
title: node的memory-cache模块
date: 2019-02-20 15:56:10
tags:
- node
- memory-cahce
- node的全局缓存
categories:
- 文档翻译
---
A simple in-memory cache for node.js
这是一个运用内存进行缓存的node.js模块

## 安装

    npm install memory-cache --save

## 用法

<!-- more -->

```javascript
var cache = require('memory-cache');

// 开始使用缓存

cache.put('foo', 'bar');
console.log(cache.get('foo'));


cache.put('houdini', 'disappear', 100, function(key, value) {
    console.log(key + ' did ' + value);
}); // Time in ms

console.log('Houdini will now ' + cache.get('houdini'));

setTimeout(function() {
    console.log('Houdini is ' + cache.get('houdini'));
}, 200);


// create new cache instance
var newCache = new cache.Cache();

newCache.put('foo', 'newbaz');

setTimeout(function() {
  console.log('foo in old cache is ' + cache.get('foo'));
  console.log('foo in new cache is ' + newCache.get('foo'));
}, 200);
```

打印结果如下

    bar
    Houdini will now disappear
    houdini did disappear
    Houdini is null
    foo in old cache is baz
    foo in new cache is newbaz

## API

### put = function(key, value, time, timeoutCallback)

* key 键
* value 值
* time 时间，以ms为单位
* timeoutCallback 这是一个可选的回调函数，在条目过期后，传入key与value进行触发(`function(key, value) {}`)
* Returns the cached value

### get = function(key)

* 传入键，即可检索相应的值
* 如果没有缓存，则返回`null`

### del = function(key)

* 删除一个键，返回一个boolean，以确定是否成功删除此健

### clear = function()

* 删除所有的键

### size = function()

* 返回缓存的当前状态下的条目数量,源码如下：
``` js
this.size = function() {
    return _size;
  };
```

### memsize = function()

* 马上在内存中检索条目数
* 使用 `== size()` 去判断是否相等，然后使用 `setTimeout` 去移除错误，源码如下：

``` js
this.memsize = function() {
    var size = 0,
      key;
    for (key in _cache) {
      size++;
    }
    return size;
  };
```

### debug = function(bool)

* 开启调试模式

### hits = function()

* 在调试模式下，返回命中的数量

### misses = function()

* 在调试模式下，返回未命中的数量

### keys = function()

* 返回所有的键

### exportJson = function()

* 以JSON格式返回所有的缓存数据
* 所有timeoutCallbacks将被忽略

### importJson = function(json: string, options: { skipDuplicates: boolean })

* 合并以前导出的所有数据
* 导入之前的任何现有条目都将保留在缓存中
* 所有重复的键都将被覆盖, 除非 `skipDuplicates` 为 `true`
* 被导出的所有条目都会在导入时到期(但是它们的回调函数都不会被调用)
* 可选 `options`:
  * `skipDuplicates`: 为 `true`, 所有重复的键会被忽略. 默认 to `false`.
* 返回最新的size

### Cache = function()

* Cache 构造函数
* 当声明模块导入 `require('cache')` 就会返回一个默认的实例
* 而 `require('cache').Cache` 则是一个类