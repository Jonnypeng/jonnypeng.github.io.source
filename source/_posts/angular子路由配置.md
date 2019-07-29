---
title: angular子路由配置
date: 2019-02-14 14:35:55
tags:
- angular
- angular router
- angular 路由
categories:
- 代码片段
---

子路由是组件下新增一个子路由组件，可以通过指令完成初始化

``` bash
ng generate component crisis-center/crisis-center
```

把组件模板修改成这样：

``` html
<h2>CRISIS CENTER</h2>
<router-outlet></router-outlet>
```

CrisisCenterComponent 和 AppComponent 有下列共同点：
* 它是危机中心特性区的根，正如 AppComponent 是整个应用的根。
* 它是危机管理特性区的壳，正如 AppComponent 是管理高层工作流的壳。

<!-- more -->

完整例子

AppRoutingModule
``` typescript
const routes: Routes = [
  {path:'',redirectTo:'/home',pathMatch:'full'},
  {path:'home',component:HomeComponent},
  {path:'product/:id',component:ProductComponent,children:[
    {path:'',component:ProductDescComponent},
    {path:'seller/:id',component:SellerInfoComponent}
  ]},
  {path:'**',component:Code404Component}
];
```

productComponent
``` html
<p>
  这是一个商品详情组件
</p>
<p>
  商品ID是{{productId}}
</p>
<a [routerLink]="['./']">商品描述</a><br>
<a [routerLink]="['./seller',99]">销售员信息</a>
<router-outlet></router-outlet>
```


seller-info.component.html
``` html
<p>
  销售员ID是{{sellerId}}
</p>
```

sellerInfoComponent
``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-seller-info',
  templateUrl: './seller-info.component.html',
  styleUrls: ['./seller-info.component.css']
})
export class SellerInfoComponent implements OnInit {
  private sellerId:number;
  constructor(private routeInfo:ActivatedRoute) { }

  ngOnInit() {
    this.sellerId = this.routeInfo.snapshot.params['id'];
  }

}
```

