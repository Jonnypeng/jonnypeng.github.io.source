---
title: angular传参的两种模式
date: 2019-02-14 11:30:19
tags:
- angular
- angular router
- angular 路由
categories:
- 代码片段
---
### 第一种，使用path/router?id=的模式
<!-- more -->
跳转页面的入口
``` html
<a [routerLink]="['/product']" [queryParams]="{id:1}">PRODUCT</a>
```
被跳转页面的控制器
``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit {

  private productId:number;
  constructor(
    private routeInfo: ActivatedRoute
  ) { }

  ngOnInit() {
    this.productId = this.routeInfo.snapshot.queryParams["id"];
  }

}
```

### 第二种，使用path/id的模式

路由配置文件
``` typescript
const routes: Routes = [
  {path:'',component:HomeComponent},
  {path:'product/:id',component:ProductComponent},
  {path:'**',component:Code404Component}
;
```

跳转页面
``` html
<a [routerLink]="['/product',1]">PRODUCT</a>
```

被跳转控制器,参数快照模式
``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit {

  private productId:number;
  constructor(
    private routeInfo: ActivatedRoute
  ) { }

  ngOnInit() {
    this.productId = this.routeInfo.snapshot.params["id"];
  }

}
```

跳转控制器
``` typescript
this.router.navigate(['/product',3]);
```

被跳转控制器，参数订阅模式
``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute ,Params} from '@angular/router';

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.css']
})
export class ProductComponent implements OnInit {

  private productId:number;
  constructor(
    private routeInfo: ActivatedRoute
  ) { }

  ngOnInit() {
    this.routeInfo.params.subscribe(
      (params:Params) => this.productId = params["id"]
    )
  }

}
```
