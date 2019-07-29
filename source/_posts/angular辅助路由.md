---
title: angular辅助路由
date: 2019-02-14 15:34:01
tags:
- angular
- angular router
- angular 路由
categories:
- 代码片段
---

辅助路由是指在路由出口router-outlet中指定一个name标识符，从而将相应的组件映射道匹配的name的路由上，看路由配置

``` typescript
const routes: Routes = [
  {path:'',redirectTo:'/home',pathMatch:'full'},
  {path:'chat',component:ChatComponent,outlet:'aux'},
  {path:'home',component:HomeComponent},
  {path:'product/:id',component:ProductComponent,children:[
    {path:'',component:ProductDescComponent},
    {path:'seller/:id',component:SellerInfoComponent}
  ]},
  {path:'**',component:Code404Component}
];
```

入口模版
``` html
<a [routerLink]="['/']">主页</a><br/>
<a [routerLink]="['/product',1]">商品详情</a>
<input type="button" value="商品详情" (click)="toProductDetails()">
<a [routerLink]="[{outlets:{aux:'chat'}}]">开始聊天</a>
<a [routerLink]="[{outlets:{aux:null}}]">结束聊天</a>
<router-outlet></router-outlet>
<router-outlet name="aux"></router-outlet>
```

当点击开始聊天时，chat组件就会映射到匹配name的相应路由出口上去,在配置辅助路由同时，也可以配置主路由
``` html
<a [routerLink]="[{outlets:{primary:'home',aux:'chat'}}]">开始聊天</a>   
<router-outlet></router-outlet>
<router-outlet name="aux"></router-outlet>
```
