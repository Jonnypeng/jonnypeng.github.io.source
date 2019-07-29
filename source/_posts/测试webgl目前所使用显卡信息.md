---
title: 测试webgl目前所使用显卡信息
date: 2019-05-29 15:51:28
tags:
- webgl
categories:
- 代码片段
---
如题

``` js
gl=document.createElement('canvas').getContext('webgl');gl.getParameter(gl.getExtension('WEBGL_debug_renderer_info').UNMASKED_RENDERER_WEBGL)
```

