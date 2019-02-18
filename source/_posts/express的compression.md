---
title: express的compression
date: 2019-02-18 14:10:26
tags:
- node
- express
- compress
- gizp
categories:
- 文档翻译
---
Node.js compression middleware.
Node.js 压缩中间件。

The following compression codings are supported:
将支持以下压缩编码：
  - deflate
  - gzip

## Install
## 安装

```bash
$ npm install compression
```

## API

```js
var compression = require('compression')
```
<!-- more -->

### compression([options])

使用options传入函数，将返回一个压缩中间件，并且将尝试去压缩所有请求的响应体，这一切都基于options。

如果客户端在http请求中明确Cache-Control的值为no-transform,那么这个中间件将无法进行压缩转换。

#### Options

`compression()` 将接受一个对象`options`,

##### chunkSize

默认值为 `zlib.Z_DEFAULT_CHUNK`, 或 `16384`.

查看详细用法 [Node.js documentation](http://nodejs.org/api/zlib.html#zlib_memory_usage_tuning)

##### filter

通过一个函数来决定是否应该压缩，函数如`filter(req, res)`,当预期返回true时，则执行压缩，当预期返回false，则不执行压缩

默认函数将通过`res.getHeader('Content-Type')`来确定是否可以压缩

##### level

压缩级别越高，压缩更多，耗费时间更长，压缩级别越低，压缩更少，耗费时间更短。

值为0(没有压缩)到9(最大压缩)之间的整数，而－1则表示默认级别6，这是一个速度与性能的折中方案。

  - `-1` 默认压缩级,等于6
  - `0` 无压缩(相当于 `zlib.Z_NO_COMPRESSION`).
  - `1` 最快的压缩 (相当于 `zlib.Z_BEST_SPEED`).
  - `2`
  - `3`
  - `4`
  - `5`
  - `6` 折中方案(相当于 `zlib.Z_DEFAULT_COMPRESSION` ).
  - `7`
  - `8`
  - `9` 最佳压缩(相当于 `zlib.Z_BEST_COMPRESSION`).

默认值为 `zlib.Z_DEFAULT_COMPRESSION`, 或 `-1`.

**Note** 以上列表, `zlib` 来自于 `zlib = require('zlib')`.

##### memLevel

在1到9之间的整形范围中取值，以指定需要多大的内存来配置内部压缩状态

默认值为`zlib.Z_DEFAULT_MEMLEVEL`, 或 `8`.

##### strategy

这是用来指定压缩的算法，值将影响压缩比，如果设置不当，将没有精确的压缩输出

  - `zlib.Z_DEFAULT_STRATEGY` 用于普通数据
  - `zlib.Z_FILTERED` 用于过滤器生成的数据，过滤的数据主要包含具有一定随机分布的小值。在这种情况下，调整压缩算法以更好地压缩它们。效果是强制更多的霍夫曼编码和更少的字符串匹配;它介于`zlib.Z_DEFAULT_STRATEGY`
    与 `zlib.Z_HUFFMAN_ONLY`.
  - `zlib.Z_FIXED` 用于防止霍夫曼编码，适用于特殊的应用程序
  - `zlib.Z_HUFFMAN_ONLY` 强制使用霍夫曼编码（无字符串匹配）
  - `zlib.Z_RLE` 用于距离限制为1，适用于游程编码（run-length encoding）. 它与`zlib.Z_HUFFMAN_ONLY`一样快, 但是对png文件可以更好地进行压缩

**Note** 以上列表, `zlib` 来自 `zlib = require('zlib')`.

##### threshold

响应的压缩前响应体大小的字节阙值，默认为`1kb`.

##### windowBits

默认值为`zlib.Z_DEFAULT_WINDOWBITS`, 或 `15`.

#### .filter

这是过滤器函数，用来自定义一个过滤器来扩展默认的过滤器函数.

```js
var compression = require('compression')
var express = require('express')

var app = express()
app.use(compression({filter: shouldCompress}))

function shouldCompress (req, res) {
  if (req.headers['x-no-compression']) {
    // 拒绝压缩
    return false
  }

  // 返回标准压缩方法
  return compression.filter(req, res)
}
```

## Examples

### express/connect

当你使用express或connect时，简单使用`app.use`, 所有的请求都会通过中间件进行压缩

```js
var compression = require('compression')
var express = require('express')

var app = express()

// 压缩所有响应
app.use(compression())

// 增加所有路由
```

### Server-Sent Events

由于压缩的性质，该模块不能开箱即用 与服务器发送的事件。要压缩内容，需要输出窗口 缓冲以获得良好的压缩。通常在使用服务器发送`res.flush()`，需要到达客户端的某些数据块

```js
var compression = require('compression')
var express = require('express')

var app = express()

// 压缩响应
app.use(compression())

// 发送实时流
app.get('/events', function (req, res) {
  res.setHeader('Content-Type', 'text/event-stream')
  res.setHeader('Cache-Control', 'no-cache')

  var timer = setInterval(function () {
    res.write('data: ping\n\n')
    res.flush()
  }, 2000)

  res.on('close', function () {
    clearInterval(timer)
  })
})
```