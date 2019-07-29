---
title: webpack简易配置
date: 2019-07-18 18:51:15
tags:
- webpack
category:
- 代码片段
---

目录结构
```
webpack-demo
  |- tsconfig.json
  |- webpack.config.js
  |- /dist
    |- bundle.js
    |- index.html
  |- /src
   |- index.ts
   |- /node_modules 
```

tsconfig
``` json
{
  "compilerOptions": {
    "outDir": "./dist/",
    "noImplicitAny": true,
    "module": "es6",
    "target": "es5",
    "jsx": "react",
    "allowJs": true,
    "sourceMap":true
  }
}
```

<!-- more -->

webpack.config.js
``` js
const path = require("path");
module.exports = {
    entry: "./src/index.ts",
    devtool: "inline-source-map",
    module: {
        rules: [
            {
                test: /\.tsx?$/,
                use: 'ts-loader',
                exclude: /node_modules/
            }
        ]
    },
    resolve:{
        extensions:[".tsx",".ts",".js"]
    },
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname, 'dist')
    }
}

```