---
title: angular路由守卫
date: 2019-02-14 15:50:29
tags:
- angular
- angular router
- angular 路由
categories:
- 代码片段
---

任何用户都能在任何时候导航到任何地方。 但有时候这样是不对的。
* 该用户可能无权导航到目标组件。
* 可能用户得先登录（认证）。
* 在显示目标组件前，你可能得先获取某些数据。
* 在离开组件前，你可能要先保存修改。
* 你可能要询问用户：你是否要放弃本次更改，而不用保存它们？

你可以往路由配置中添加守卫，来处理这些场景。 守卫返回一个值，以控制路由器的行为：
* 如果它返回 true，导航过程会继续
* 如果它返回 false，导航过程就会终止，且用户留在原地。
* 如果它返回 UrlTree，则取消当前的导航，并且开始导航到返回的这个 UrlTree.

*注意：守卫还可以告诉路由器导航到别处，这样也会取消当前的导航。要想在守卫中这么做，就要返回 false*

<!-- more -->

先定一个类进行路由守卫

以下是进入路由的守卫
``` typescript
import { CanActivate } from "@angular/router";

export class LoginGuard implements CanActivate{
    canActivate(){
        let loggedIn:boolean = Math.random() > 0.5;

        if(!loggedIn){
            console.log("用户未登录");
        }
        return loggedIn;
    }
}
```

以下是出路由的守卫
``` typescript
import { CanDeactivate } from "@angular/router";
import { ProductComponent } from "../product/product.component";

export class UnsavedGuard implements CanDeactivate<ProductComponent>{
    canDeactivate(component:ProductComponent){
        return window.confirm("你还没有保存，确定要离开吗？");
    }
}
```

再进行路由配置
``` typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { ProductComponent } from './product/product.component';
import { Code404Component } from './code404/code404.component';
import { ProductDescComponent } from './product-desc/product-desc.component';
import { SellerInfoComponent } from './seller-info/seller-info.component';
import { ChatComponent } from './chat/chat.component';
import { LoginGuard } from './guard/login_guard';
import { UnsavedGuard } from './guard/unsaved_guard';

const routes: Routes = [
  {path:'',redirectTo:'/home',pathMatch:'full'},
  {path:'chat',component:ChatComponent,outlet:'aux'},
  {path:'home',component:HomeComponent},
  {path:'product/:id',component:ProductComponent,children:[
    {path:'',component:ProductDescComponent},
    {path:'seller/:id',component:SellerInfoComponent}
  ],canActivate:[LoginGuard],canDeactivate:[UnsavedGuard]},
  {path:'**',component:Code404Component}
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers:[LoginGuard,UnsavedGuard]
})
export class AppRoutingModule { }
```