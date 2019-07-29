---
title: pixi的webgl导出异常
date: 2019-05-24 10:04:42
tags:
- PIXI
- WebGL
categories:
- 代码片段
---

Canvas导出图片是没有问题的，但是WebGL模式下导出图片，在IOS上会导致上下逆转，如何解决这个问题，PIXI有专门的插件
``` javascript
const img = app.renderer.plugins.extract.image(app.stage);
```
这样的话，就不用先导出base64，然后再创建图片，而且也不会发生上下逆转