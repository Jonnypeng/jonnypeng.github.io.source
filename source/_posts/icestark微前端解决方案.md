---
title: icestark微前端解决方案
date: 2021-09-30 14:02:30
tags:
---

# 快速起步

## 介绍

[icestark](https://github.com/ice-lab/icestark) 是一个面向大型系统的微前端解决方案，适用于以下业务场景：

- 后台比较分散，体验差别大，因为要频繁跳转导致操作效率低，希望能统一收口的一个系统内
- 单页面应用非常庞大，多人协作成本高，开发/构建时间长，依赖升级回归成本高
- 系统有二方/三方接入的需求

icestark 在保证一个系统的操作体验基础上，实现各个微应用的独立开发和发版，主应用通过 icestark 管理微应用的注册和渲染，将整个系统彻底解耦。

<!--more-->

## 项目初始化

icestark 可以通过简单的命令行，生成主应用和微应用模板。无论您是使用 React 还是 Vue，都可以便捷的创建符合 icestark 微前端规范的项目。这些项目均由 icestark 团队官方维护。

:::tip
如果您想将正在开发中或已开发完成的项目接入 icestark，请移步 <a href="/docs/guide/use-layout/react">主应用接入</a> 和 <a href="/docs/guide/use-child/react">微应用接入</a>。如果您使用的是 <a href="https://github.com/facebook/create-react-app">create-react-app</a> 、umi 等框架开发的应用，亦可参考 <a href="/docs/guide/use-child/others">其他框架的接入指南</a>。
:::

### 初始化主应用

```bash
# 基于 React 的主应用
$ npm init ice icestark-layout @icedesign/stark-layout-scaffold
# 或者基于 Vue 的主应用
$ npm init ice icestark-layout @vue-materials/icestark-layout-app

$ cd icestark-layout
$ npm install
$ npm start
```

### 初始化微应用

```bash
# 基于 React 的微应用
$ npm init ice icestark-child @icedesign/stark-child-scaffold
# 基于 Vue 的微应用
$ npm init ice icestark-child @vue-materials/icestark-child-app

$ cd icestark-child
$ npm install
$ npm run start
```

## 兼容性

+ 现代浏览器和 IE11。

:::tip
对于 IE 系列浏览器，需要提供相应的 polyfill 支持。详细介绍，请参考 <a href="/docs/faq#兼容-ie-浏览器"> 常见问题 -> 兼容 IE 浏览器 </a>
:::

# 主应用

又称框架应用或基座应用，一个系统只有一个主应用。主应用应该保持职责明确，一个设计良好的主应用只做两件事情：

1. 系统整体 Layout 的设计

2. 所有微应用的配置与注册


主应用尽量避免包含具体页面的 UI 代码，如果主应用做了过多的事情会带来以下问题：

+ 主应用样式代码太多，会增加微应用和主应用样式冲突概率

+ 主应用为微应用提供其他能力比如一些全局 API，会破坏微应用的独立性，增加相互的耦合

+ 主应用本质是一个中心化的部分，变更后原则上需要回归所有微应用，因此需要保证职责的简单，越简单的东西越稳定

# React 主应用接入

对于 React 主应用，推荐使用 `AppRouter/AppRoute` 这种 React Component 的方式。

## 通过官方脚手架创建

> 该官方脚手架基于 [icejs](https://ice.work/) 框架，如需使用其他框架/工程可按 [已有应用改造](./react#已有应用改造) 的方式接入。

```bash
$ npm init ice icestark-layout @icedesign/stark-layout-scaffold
$ cd icestark-layout
$ npm install
$ npm start
```

:::info 提示
更多有关官方脚手架的使用细节，请访问 [飞冰 - 微前端 icestark](https://ice.work/docs/guide/advanced/icestark)
:::


## 已有应用改造

### 微应用管理与注册

通过 `AppRouter/AppRoute` 注册微应用：

```js
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';
import BasicLayout from '@/layouts/BasicLayout';

export default class App extends React.Component {
  render() {
    return (
      <BasicLayout>
        <AppRouter>
          <AppRoute
            title="商家平台"
            url={[
              '//unpkg.com/icestark-child-seller/build/js/index.js',
              '//unpkg.com/icestark-child-seller/build/css/index.css',
            ]}
          />
          <AppRoute
            path="/user"
            //...
          />
        </AppRouter>
      </BasicLayout>
    );
  }
}
```

通过 ReactDOM 渲染应用：

```js
// src/index.jsx
import ReactDOM from 'react-dom';
import React from 'react';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```

### AppRouter 配置项

- onRouteChange：路由变化时触发
- NotFoundComponent：未匹配到微应用时渲染的组件
- ErrorComponent：微应用加载失败渲染的组件
- LoadingComponent：微应用加载过程渲染的组件
- basename：整个主应用部署在某个基础路由下

更多参数请参考 [API 介绍 AppRouter](/docs/api/ice-stark#approuter)。

### AppRoute 配置项

#### 路由与微应用的映射关系

通过 path 和 exact 两个属性配置微应用的匹配规则，建立路由和微应用的映射关系。

```js
// 匹配符合 /seller/* 的所有路由
<AppRoute
  path="/seller"
/>

// 仅完全匹配 /seller 这一个路由
<AppRoute
  path="/seller"
  exact
/>

// 匹配符合 /seller/* 以及 /user/* 的所有路由
<AppRoute
  path={['/seller', '/user']}
/>

// 匹配 /seller 这一个路由以及符合 /user/* 的所有路由
<AppRoute
  path={[
    { path: '/seller', exact: true },
    { path: '/user', exact: false },
  ]}
/>
```

#### 微应用入口 entry/entryContent/url

对于 entry/entryContent/url 方式的使用请参考文档[微应用 - 入口规范](/docs/guide/concept/child#入口规范)。在此基础上，结合 React 的能力 AppRoute 还支持了更为灵活的 component 和 render 方式配置微应用入口：

```js
<AppRoute
  path="/foo"
  render={() => {
    return <iframe src="" />;
  }}
  // 或者直接传入 component
  // component={CustomComponent}
/>
```

通过这种方式，可以通过 AppRoute 渲染一个 iframe 或者任意的 React 组件（比如 ReactRouter）。

#### 其他参数

- umd：默认 `false`，标识微应用是否为 umd 类型
- sandbox：默认 `false`，是否开启 js 沙箱模式
- cache：默认 `false`，切换后是否缓存微应用，可能会引起内存占用过大问题，不建议开启
- hashType：默认 `false`，标识微应用的路由是否为 hash 模式

更多参数请参考 [API 介绍 AppRoute](/docs/api/ice-stark#approute)。


## 进阶指南

### 主应用中路由跳转

如果在主应用里定义了一些路由页面，那么微前端主应用的层级如下：

```js
<FrameworkLayout>
  <AppRouter>
    <AppRoute path="/seller" url={[]} />
    <AppRoute path="*" component={() => {
      return <ReactRouter></ReactRouter>
    }} />
  </AppRouter>
<FrameworkLayout>
```

这里面可以看到 FrameworkLayout 是在 ReactRouter 外层的，因此在 FrameworkLayout 里是没法使用 react-router 提供的 API，比如 `withRouter`, `Link`, `useParams` 等。如果要做路由跳转建议通过 `appHistory` 或者 `AppLink` 来实现：

```diff
import React from 'react';
+import { appHistory, AppLink } from '@ice/stark-app';

export default function FrameworkLayout() {
  return (
    <>
      <span
        onClick={() => {
+          appHistory.push('/seller/list');
        }}
      >
        seller
      </span>
+      <AppLink to="/waiter/list">waiter</AppLink>
    </>
  );
}
```

### 主应用中如何包含路由页面

我们不推荐主应用里包含一些具体的路由页面，但是如果业务有这种需求，比如想把登录注册之类的通用页面放在主应用里，可以按照下面的方式。

> 注意：使用官方模板创建出来的 icejs 主应用默认支持该能力

```diff
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';
+import { Router, Switch, Route } from 'react-router';
import BasicLayout from '@/layouts/BasicLayout';
+import Home from './pages/Home';
+import Login from './pages/Login';

export default class App extends React.Component {
  render() {
    return (
      <BasicLayout>
        <AppRouter>
          <AppRoute path="/seller" />
          <AppRoute path="/user" />
+          <AppRoute
+            path="*"
+            render={() => {
+              return (
+                <Router>
+                  <Switch>
+                    <Route path="/login" component={Login} />
+                    <Route path="/" exact component={Home} />
+                    <Route path="*" exact component={NotFound} />
+                  </Switch>
+                </Router>
+              );
+            }}
+          />
        </AppRouter>
      </BasicLayout>
    );
  }
}
```

### 通过数据驱动注册微应用列表

在很多场景下，我们的微应用可能通过一些配置平台注册，这时候可以将所有微应用的信息通过全局变量输出到 html 中，然后前端通过该数据注册微应用：

```js
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';

export default class App extends React.Component {
  render() {
    return (
      <AppRouter>
        {
          (window.appConfig || []).map((item) => {
            return (
              <AppRoute
                key={idx}
                path={item.path}
                title={item.title}
                url={item.url}
              />
            );
          })
        }
      </AppRouter>
    );
  }
}
```

### 微应用级别权限校验

icestark 支持对 `AppRoute` 再进行二次封装，统一处理容器定制/权限校验等场景：

```js
// src/components/AuthAppRoute.js
import React, { useState, useEffect } from 'react';
import { AppRoute } from '@ice/stark';

export default function AuthAppRoute(props) {
  const [loading, setLoading] = useState(true);
  const [hasAuth, setHasAuth] = useState(false);

  useEffect(() => {
    setTimeout(() => {
      setHasAuth(true);
      setLoading(false);
    }, 1000);
  }, []);

  if (loading) {
    return <div>wait for a moment</div>;
  }

  if (!hasAuth) {
    return <div>No access!</div>;
  }

  return (
    <div>
      //  注意要将 ...others 透传给 AppRoute
      <AppRoute {...props} />
    </div>
  );
}
```

封装完成后即可在 AppRouter 下使用：

```js
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';
import NotFound from '@/components/NotFound';
import PageLoading from '@/components/PageLoading';
import BasicLayout from '@/layouts/BasicLayout';
import AuthAppRoute from '@/components/AuthAppRoute';

export default class App extends React.Component {
  render() {
    return (
      <BasicLayout>
        <AppRouter
          NotFoundComponent={NotFound}
          LoadingComponent={PageLoading}
        >
          {/* 注意：path/url/entry 等配置信息配置在组件外层，AppRouter 的直接子元素上 */}
          <AuthAppRoute
            path={['/', '/message', '/about']}
            exact
            title="主页"
            url={[
              '//unpkg.com/icestark-child-common/build/js/index.js',
              '//unpkg.com/icestark-child-common/build/css/index.css',
            ]}
          />
        </AppRouter>
      </BasicLayout>
    );
  }
}
```

### 不同页面 Layout 不同

通过 AppRouter 的 `onRouteChange` 属性可以捕获到所有的路由变化，此时可以根据不同路由对 Layout 做一些状态的变化，实现不同页面不同布局的能力。

```js
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';

export default class App extends React.Component {
  state = {
    pathname: '',
  }

  handleRouteChange = (pathname) => {
    console.log('route change', pathname);
    // 如有需求，可根据 pathname 切换 layout 的形态
    this.setState({
      pathname,
    });
  }

  render() {
    const { pathname } = this.state;

    return (
      <!-- BasicLayout 可根据 pathname 属性切换展现形式 -->
      <BasicLayout pathname={pathname}>
        <AppRouter
          onRouteChange={this.handleRouteChange}
        >
          <AppRoute />
        </AppRouter>
      </BasicLayout>
    );
  }
}
```
import Badge from '../../../src/components/Badge'

# 微应用

又称子应用，微应用通常是一个单页面应用（SPA），可能包含一个或多个路由页面，一般情况下不存在多个微应用同时运行的场景。有以下特点：

+ 本身是普通的前端应用，负责具体的业务逻辑；
+ 可以独立交付（开发、部署、运行），但是一般会集成到主应用中运行；
+ 如有必要，甚至能集成到不同的主应用中。

## 生命周期

在 icestark 中，微应用是一个 **具有生命周期** 的前端资源。icestark 中有两个生命周期，分别是：

+ 微应用挂载到主应用
+ 微应用从主应用卸载

icestark 支持两种声明生命周期的方式。分别通过全局注册的 `registerAppEnter/registerAppLeave` 以及 UMD 格式下导出的 `mount/unmount` 方法。为了跟社区的 [single-spa](https://single-spa.js.org/) 方案更好兼容，我们推荐使用后者。

#### 1. registerAppEnter/registerAppLeave

```js
import ReactDOM from 'react-dom';
import { getMountNode, registerAppEnter, registerAppLeave } from '@ice/stark-app';
import App from './App';

registerAppEnter(() => {
  ReactDOM.render(<App />, getMountNode());
});

registerAppLeave(() => {
  ReactDOM.unmountComponentAtNode(getMountNode());
});
```

#### 2. mount/unmount <Badge text="1.6.0+" />

```js
import ReactDOM from 'react-dom';
import App from './App';

export function mount(props) {
  ReactDOM.render(<App />, props.container);
}

export function unmount(props) {
  ReactDOM.unmountComponentAtNode(props.container);
}
```

## 入口规范

icestark 通过微应用入口字段的配置进行应用的渲染，因此这个字段 **非常重要**。针对不同的场景，icestark 也支持了多种入口配置形式。

### 1. url

适用于微应用入口资源比较确定，此时将这些资源地址按顺序拼成数组传给 icestark 即可。

```js
const apps = [{
  url: ['https://example.com/a.js', 'https://example.com/a.css'],
  path: '/foo'
  // ...
}]
```

### 2. entry

使用场景：

- 应用依赖的入口资源不确定：比如需要引入 vendor、或者不确定的 externals 资源、资源地址带 hash 等场景
- 应用默认需要依赖很多 DOM 节点：比如 `jQuery`/`Kissy`/`Angular` 等框架

```js
const apps = [{
  entry: 'https://example.com/a.html',
  path: '/foo'
  // ...
}]
```

entry 对应 html url, icestark 对 `entry` 的处理包含以下步骤：

- 通过 `window.fetch` 获取 entry 属性对应的 html 内容
- 解析 html 内容，框架将会进行解析处理：提取 js 信息，如果资源路径为相对地址，将根据 entry 地址进行补齐
- 将处理后的 html 内容插入 icestark 动态创建的节点
- 依次通过创建 `script` 标签按顺序引入 js 资源

### 3. entryContent

当需要使用 entry 能力但是 html url 不支持前端跨域访问的情况，可以自行将 html 内容拿到，然后通过 entryContent 属性传递给 icestark。

```js
const apps = [{
  entryContent: '<!DOCTYPE html><html><body><script src=""></body></html>',
  path: '/foo'
  // ...
}]
```

### 4. render/component

仅使用 React 的主应用支持，具体请参考 [主应用接入 - React](/docs/guide/use-layout/react)。

# React 微应用接入

## 通过脚手架创建

> 该官方脚手架基于 [icejs](https://ice.work/) 框架。

```bash
$ npm init ice icestark-child @icedesign/stark-child-scaffold
```

:::info 提示
更多有关官方脚手架的使用细节，请访问 [飞冰 - 微前端 icestark](https://ice.work/docs/guide/advanced/icestark)
:::

## 已有 React 项目改造为微应用

### 1. 应用入口适配

将 React 应用改造为微应用，只需要导出对应的生命周期即可：

```js
import ReactDOM from 'react-dom';
import { isInIcestark, setLibraryName } from '@ice/stark-app';
import App from './App';

export function mount(props) {
  ReactDOM.render(<App {...customProps} />, props.container);
}

export function unmount(props) {
  ReactDOM.unmountComponentAtNode(props.container);
}

// 注意：`setLibraryName` 的入参需要与 webpack 工程配置的 output.library 保持一致
setLibraryName('microApp');

if (!isInIcestark()) {
  ReactDOM.render(<App />, document.getElementById('ice-container'));
}
```

### 2. 定义基准路由

正常情况下，注册微应用时会为每个微应用分配一个基准路由比如 `/seller`，当前微应用的所有路由需要定义在基准路由之下，社区常见的路由库都可以通过参数非常简单的实现该功能。微应用可以通过 `getBasename()` API 获取自身的基准路由。

React 项目中使用 `react-router`：

```diff
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Redirect } from 'react-router-dom';
+import { getBasename } from '@ice/stark-app';

export default () => {
  return (
+   <Router basename={getBasename()}>
      <Switch>
        // ...
      </Switch>
    </Router>
  );
};
```

### 3. 构建为 UMD 产物

入口文件通过导出 `mount`、`unmount` 等标准生命周期后，需要配置工程上的改造，才能最终导出 UMD 标准的微应用。

以 webpack 工程为例：

```js
module.exports = {
  output: {
    // 设置模块导出规范为 umd
    libraryTarget: 'umd',
    // 可选，设置模块在 window 上暴露的名称
    library: 'microApp',
  }
}
```

# 微模块

微应用的更小粒度，通常是一个模块或页面，跟页面路由无关，可以随处挂载，也会出现多个微模块同时渲染运行。

## 通过脚手架创建

:::tip
如果是阿里内部的同学，参考文档 <a href="https://yuque.alibaba-inc.com/ice/rdy99p/mmhh1b">微模块开发接入 DEF</a>
:::

```shell
# 创建文件夹
$ mkdir micro-module & cd micro-module

# 初始化
$ iceworks init component @icedesign/template-icestark-module

# 安装依赖
$ npm install
$ npm start
```

### 模块开发

```shell
$ cd my-component
$ npm install
$ npm start
```

### 模块目录

```text
.
├── demo                  # 模块 demo
│   └── usage.md
├── src                   # 模块源码
│   ├── index.scss
│   └── index.tsx
├── lib/                  # 构建产物，编译为 ES5 的代码
├── es/                   # 构建产物，编译为 es module 规范的代码
├── dist/                 # 构建产物，编译为 umd 规范的代码
├── jest.config.js
├── build.json            # 构建配置
├── README.md
├── abc.json
├── package.json
└── tsconfig.json
```

#### 模块入口

模块入口文件为 `src/index.js` 。

```javascript
import React from 'react';

export default function ExampleComponent(props) {
  const { type, ...others } = props;

  return (
    <div className="ExampleComponent" {...others}>Hello ExampleComponent</div>
  );
}
```

#### 模块配置

模块开发工程需要在 `build.json` 中引入 `build-plugin-component` 和 `build-plugin-stark-module` 。

```json
// build.json
{
  "plugins": [
    "build-plugin-component",
    ["build-plugin-stark-module", {
      // ...options
    }]
  ]
}
```

`build-plugin-component` 配置请参考 [组件工程配置](https://ice.work/docs/materials/guide/component#%E7%BB%84%E4%BB%B6%E5%B7%A5%E7%A8%8B%E9%85%8D%E7%BD%AE)。 `build-plugin-stark-module` 的工程配置如下：

##### outputDir

构建结果目录。

- 类型： String
- 默认值： 'dist'
```json
// build.json
{
  "plugins": [
    ["build-plugin-stark-module", {
      "outputDir": 'build' // umd 构建结果打包至项目 build 目录
    }]
  ]
}
```
##### modules

配置多微模块入口。正常情况下，插件默认使用 `src/index.js` 作为微模块入口。


- 类型： object
- 默认值： {}

```json
// build.json
{
  "plugins": [
    ["build-plugin-stark-module", {
      "modules": {
        "branch-detail": "./src/branch-detail/index.tsx",
        "edit-info": "./src/edit-info/index.tsx"
      }
    }]
  ]
}
```

如上配置后，会在构建目录打包两个微模块资源。

```
.
├── branch-detail
│   ├── index.css
│   └── index.js
├── edit-info
	  ├── index.css
	  └── index.js
```

##### moduleExternals

构建时移除三方依赖。详见 [使用进阶 - 性能优化](/docs/guide/micro-module#性能优化) 小节。

- 类型：object
- 默认值： {}

```json
{
  "plugins": [
    ...
    ["build-plugin-stark-module", {
      "moduleExternals": {
        "react": {
          "root": "React",
          "url": "https://g.alicdn.com/code/lib/react/16.14.0/umd/react.production.min.js",
        },
        "react-dom": {
          "root": "ReactDOM",
          "url": "https://g.alicdn.com/code/lib/react-dom/16.14.0/umd/react-dom.production.min.js"
        }
      }
    }],
  ...
  ]
}

```

## 已有项目改造为微模块

将已有项目改造为微模块的方式与 [微应用](/docs/guide/use-child/react) 类似，主要包含两步：

#### 1. 应用入口导出生命周期函数

下面是 React 模块的导出方式：

```js
import * as ReactDOM from 'react-dom';

const SampleModule = () => {
  return <div>Sample</div>;
}

// 声明 mount 生命周期
export function mount(ModuleComponent, targetNode, props) {
  ReactDOM.render(<ModuleComponent {...props} />, targetNode);
}

// 声明 unmount 生命周期
export function unmount(targetNode) {
  ReactDOM.unmountComponentAtNode(targetNode);
}

export default SampleModule;
```

下面是 Vue 2.x 模块导出的方式：

```js
import Vue from 'vue';
import SampleModule from './SampleModule';

let vue = null;

// 声明 mount 生命周期
export function mount(ModuleComponent, targetNode, props) {
  vue = new Vue({
    components: { ModuleComponent }
  }).$mount();

  // for vue don't replace mountNode
  container.innerHTML = '';
  container.appendChild(vue.$el);
}

// 声明 unmount 生命周期
export function unmount(targetNode) {
  vue && vue.$destroy();
}

export default SampleModule;
```

#### 2. 将模块构建为 UMD 产物

以 webpack 工程为例：

```js
module.exports = {
  output: {
    // 设置模块导出规范为 umd
    libraryTarget: 'umd',
    // 可选，设置模块在 window 上暴露的名称
    library: 'microApp',
  }
}
```

## 如何使用

### React 项目中使用

在 React 项目中使用微模块，我们推荐使用 `<MicroModule />` 组件快速接入。

```js
import { MicroModule } from '@ice/stark-module';

const App = () => {
  const moduleInfo = {
    name: 'moduleName',
    url: 'https://localhost/module.js',
  }
  return <MicroModule moduleInfo={moduleInfo} />;
}
```

也可以通过 `mountModule/unmountModule` 等 API 的方式接入。

```javascript
import { mountModule, unmoutModule } from '@ice/stark-module';
import { useRef } from 'useRef';

const moduleInfo = {
  name: 'moduleName',
  url: 'https://localhost/module.js',
};

const ModuleComponent = () => {
  const renderNode = useRef(null);
  useEffect(() => {
    mountModule(moduleInfo, renderNode.current, {});
    return () => {
      unmoutModule(moduleInfo, renderNode.current);
    }
  }, []);
  return (<div ref={renderNode}></div>);
};
```

### Vue 项目中使用

Vue 项目中需要使用 `mountModule/unmountModule` 的方式接入。

```html
<template>
  <div ref="mountNode"></div>
</template>

<script>
import { mountModule, unmoutModule } from '@ice/stark-module';

const moduleInfo = {
  name: 'moduleName',
  url: 'https://localhost/module.js',
};

export default {
  const mountNode = this.$refs.mountNode.$el;
	mounted () {
  	mountModule(moduleInfo, mountNode);
  },
  destroyed () {
  	unmoutModule(moduleInfo, mountNode)
  }
}
</script>
```

## 使用进阶

### 微模块中心化注册

如果需要前置中心化注册微模块，可以使用 `registerModules` 方法。

```js
import { MicroModule, registerModules, getModules } from '@ice/stark-module';

registerModules([
  {
    url: 'https://localhost/module-a.js',
    name: 'module-a',
  },
  {
    url: 'https://localhost/module-b.js',
    name: 'module-b',
  },
]);

const App = () => {
  // 中心化注册过，可以通过模块名直接指定要加载的微模块
  return (
    <div>
      <MicroModule moduleName="module-a" />
      <MicroModule moduleName="module-b" />
    </div>
  );
}
```

### 自定义生命周期

可以在微模块不导出 `mount` 和 `unmount` 的情况下，自定义生命周期。

```js
import { MicroModule } from '@ice/stark-module';
import ReactDOM from 'react-dom';

const App = () => {
  const moduleInfo = {
    name: 'moduleName',
    url: 'https://localhost/module.js',
    mount: (ModuleComponent, mountNode, props) => {
      console.log('custom mount');
      ReactDOM.render(<ModuleComponent />, mountNode, props);
    },
  }
  return <MicroModule moduleInfo={moduleInfo} />;
}
```

值得注意的是，若微模块导出了生命周期，其优先级高于自定义生命周期。

### 性能优化

通常情况下，为了减少模块体积，希望抽离一些公共的三方库。

**首先，微模块需要在构建时移除依赖的三方库。**

```javascript
// webpack.config.js
export default {
  ...
  externals: {
   react: {
      root: 'React',
      commonjs2: 'react',
      commonjs: 'react',
      amd: 'react',
    },
    'react-dom': {
      root: 'ReactDOM',
      commonjs2: 'react-dom',
      commonjs: 'react-dom',
      amd: 'react-dom',
    }
  }
}
```

**然后，在应用项目中，声明微模块的依赖**。

```javascript
import { MicroModule } from '@ice/stark-module';
import ReactDOM from 'react-dom';

const App = () => {
  const moduleInfo = {
    name: 'moduleName',
    url: 'https://localhost/module.js',
    // 声明模块 moduleName 所需的三方依赖
    runtime: [
      {
        id: "react@16",
        url: [
          "https://g.alicdn.com/code/lib/react/16.14.0/umd/react.production.min.js"
        ]
      },
      {
        id: "react-dom@16",
        url: [
          "https://g.alicdn.com/code/lib/react-dom/16.14.0/umd/react-dom.production.min.js"
        ]
      },
    ]
  }
  return <MicroModule moduleInfo={moduleInfo} />;
}
```

如果使用官方 `build-plugin-stark-module` 插件来构建的微模块，只需要在 build.json 中配置：

```javascript
{
  "plugins": [
    ...
    ["build-plugin-stark-module", {
      "moduleExternals": {
        "react": {
          "root": "React",
          "url": "https://g.alicdn.com/code/lib/react/16.14.0/umd/react.production.min.js",
        },
        "react-dom": {
          "root": "ReactDOM",
          "url": "https://g.alicdn.com/code/lib/react-dom/16.14.0/umd/react-dom.production.min.js"
        }
      }
    }],
	...
  ]
}
```

该插件会将三方依赖从模块中移除，并在产物目录生成一份依赖信息 `runtime.json`，模块发布时，需要将 `runtime.json` 一起发布。这样，在应用项目中，可以使用 `runtime.json` 作为依赖信息。

```javascript
import { MicroModule } from '@ice/stark-module';
import ReactDOM from 'react-dom';

const App = () => {
  const moduleInfo = {
    name: 'moduleName',
    url: 'https://localhost/module.js',
    // 声明模块 moduleName 需要的依赖文件地址
    runtime: 'https://xxx.com/moduleName.runtime.json'
  }
  return <MicroModule moduleInfo={moduleInfo} />;
}
```

# 应用间通信

通过 `@ice/stark-data` 这个包可以很简单的实现应用间通信，比如全局切换语言微应用响应的场景。`@ice/stark-data` 支持状态共享和事件监听响应两种方式。

## store

### API

- `get(key)` **从 store 中获取变量**
- `set(key, value)` **设置/初始化 store 中的变量**
- `on(key, callback, force)` **注册变量监听事件，其中 force 为 boolean 类型，true 则表示初始化注册过程中，会强制执行一次**
- `off(key, callback)` **删除已经注册的变量监听事件**

### 示例

使用场景：

1. 中英文切换，切换按钮在主应用，监听事件在微应用
2. 获取全局的登录用户信息

在主应用存储/设置信息：

```js
// 主应用
import { store } from '@ice/stark-data';

const userInfo = { name: 'Tom', age: 18 };
store.set('language', 'CH'); // 设置语言
store.set('user', userInfo); // 设置登录后当前用户信息

setTimeout(() => {
  store.set('language', 'EN');
}, 3000);
```

在微应用中响应/获取数据：

```js
// 微应用
import { store } from '@ice/stark-data';

// 监听语言变化
store.on('language', (lang) => {
  console.log(`current language is ${lang}`);
}, true);

// 获取当前用户
const userInfo = store.get('user');
```

## event

### api

- `on(key, callback)` **注册回调函数，回调函数的入参通过 emit 注入，如 ...rest**
- `off(key, callback)` **删除已经注册的回调函数**
- `emit(key, ...rest)` **触发已经注册的函数，支持入参**

### 示例

主应用顶部有**消息**展示入口，微应用内有阅读消息的能力，阅读完消息后需要通知主应用刷新**消息**展示信息。

在主应用中监听事件：

```js
// 主应用
import { event } from '@ice/stark-data';

event.on('freshMessage', () => {
  // 重新获取消息数
});
```

在微应用中触发事件：

```js
// 微应用
import { event } from '@ice/stark-data';

event.emit('freshMessage');
```

## 其他

对于主应用和微应用，运行时都共享了当前页面的 location、Cookie、LocalStorage、window 等全局信息，因此应用间的通信，也可以通过这些方案很简单的实现。

# 性能优化

## prefetch

通过预加载子应用资源，可以提升 **非首屏首次加载子应用** 的渲染速度。简单用法如下：

```js
import { registerMicroApps, start } from '@ice/stark';

registerMicroApps([
  {
    name: 'waiter',
   	path: '/waiter',
    title: '商家平台',
    sandbox: true,
    url: [
      'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-seller-react/build/js/index.js',
      'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-seller-react/build/css/index.css',
    ],
  },
  {
    name: 'seller',
   	path: '/seller',
    title: '小二平台',
    sandbox: true,
    url: [
      'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/js/app.js',
      'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/css/app.css',
    ],
  }
], {
	afterMount: () => { console.log('mounted') },
});

start({
	prefetch: true,
})
```

`prefetch` 值支持三种形式：

+ **布尔值**：当 `prefetch` 为 `true` 时，所以微应用资源均会被 `prefetch`;
+ **数组**：当传入值为 `name` 的数组时，只要数组中的微应用资源会被 `prefetch`;

```js
start({
	prefetch: ['waiter'], // 只有 `name` 为 waiter 的微应用会被 prefetch
})
```

+ **函数**：当传入为函数时，`prefetch` 加载的能力需要自己处理。

```js
start({
	prefetch: (app) => app.name === 'waiter',
})
```

当您使用的 `AppRouter` 接入 icestark 时，也是相同的使用方式：

```js
// src/App.jsx
import React from 'react';
import { AppRouter, AppRoute } from '@ice/stark';
import BasicLayout from '@/layouts/BasicLayout';

export default class App extends React.Component {
  render() {
    return (
      <BasicLayout>
        <AppRouter
          prefetch
          // or prefetch={['waiter']}
          // or prefetch={(app) => app.name === 'waiter'}
          >
          <AppRoute
            name="waiter"
            path="/waiter"
            title="商家平台"
            url={[
              'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/js/app.js',
              'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/css/app.css',
            ]}
          />
          ...
        </AppRouter>
      </BasicLayout>
    );
  }
}
```

还可以通过 `prefetchApps` 手动控制需要被 `prefetch` 的应用。

```js
// 在某个比较前的时机
prefetchApps([{
	name: 'waiter',
  url: [
    'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/js/app.js',
    'https://iceworks.oss-cn-hangzhou.aliyuncs.com/icestark/child-waiter-vue/dist/css/app.css',
  ]
}]);
```

:::tip
有关 <code>prefetch</code> 的更多讨论或反馈，请移步 <a href="https://github.com/ice-lab/icestark/issues/188">RFC-prefetch</a>
:::

icestark 当下的方案里，无论是主应用还是微应用都是直接在页面里执行，本质上不存在隔离机制，针对这个问题我们一方面通过一些规范来保证污染问题，一方面也在尝试更加彻底的沙箱机制，如果你的微应用都是二方接入，那我们推荐直接通过规范约束即可，如果存在三方接入这种不可控的场景，建议还是通过 iframe 的方式嵌入。

## 样式污染

页面运行时同时只会存在一个微应用，因此多个微应用不存在样式相互污染的问题，但是主应用和微应用是同时运行的，因此这两者中间可能会存在一些样式相互污染，针对这个问题，我们目前推荐「通过约定避免微应用与主应用的样式相互污染」的方案，同时也在尝试 Shadow DOM 的方案。

### 规范

#### 使用 CSS Modules 方案管理样式

无论是主应用还是微应用，直接通过 CSS Modules 的方案管理自身可控的样式，这样基本杜绝了两者样式冲突的问题。

#### 主应用自定义基础组件 prefix

除了自身可控的样式，应用中还会有一些全局样式，比较典型的就是类似 next 这种基础组件的样式，如果主应用和微应用使用了不同版本的 next，则很容易造成样式相互污染，这种场景推荐在主应用中将基础组件的前缀统一改掉，比如将 `next-` 改为 `next-icestark-`，这个能力已在主应用模板中内置，具体可参考相关代码。

#### 微应用避免产生全局样式

对于类似 `normalize.css` 这种全局重置样式，推荐统一通过主应用引入，微应用尽量避免产生全局性质的样式，因为这样在切换微应用时可能会因为全局样式差异产生一些抖动。

### Shadow DOM（方案试验中）

如果将微应用渲染到 Shadow DOM 中，那么微应用产生的所有样式都不会污染到全局，事实上在我们试验的过程中的确是这样的。但是我们遇到一个当下无法解决的问题，大部分类似 Dialog 组件的实现都是在 body 下创建一个容器节点，但是 Shadow DOM 里 Dialog 的样式无法作用到全局，因此展示出来 Dialog 就是无样式的，在这个问题上我们还在尝试，比如类似 Dialog 组件的实现能够进行优化：判断自身是否在 Shadow DOM 里，如果是的话则将容器节点创建到 Shadow DOM 里，否则创建到 body 节点下。

## JS 污染

相对于样式污染，JS 污染的危害性更高，在目前的方案下，如果微应用想要恶意污染的话基本是无法杜绝的，因此针对这种不可控的微应用建议还是通过 iframe 的方式接入。针对可控的二方应用，正常书写代码是不会有问题的，针对一些特殊情况我们也总结了一些规范。

### 规范

#### 微应用避免改变全局状态

比如改变全局变量 `window/location` 的默认行为，通过 `document` 操作 Layout 的 DOM，这些本身都是一些不推荐的做法。

#### 主应用通过钩子记录并恢复全局状态

```js
<AppRouter
  onAppEnter={(appConfig) => {
    // 按需记录全局状态
  }}
  onAppLeave={(appConfig) => {
    // 按需恢复全局状态
  }}
>
  // {...}
</AppRouter>
```

### 基于 Proxy 的运行沙箱

通过 `with + new Function` 的形式，为微应用脚本创建沙箱运行环境，并通过 Proxy 代理阻断沙箱内对 `window` 全局变量的访问和修改。

icestark 内置了基于 `@ice/sandbox` 的沙箱隔离，通过 `sandbox` 属性开启：

```js
<AppRoute
  sandbox
  path="/seller"
  title="商家平台"
  url={[
    '//unpkg.com/icestark-child-seller/build/js/index.js',
    '//unpkg.com/icestark-child-seller/build/css/index.css',
  ]}
/>
```



