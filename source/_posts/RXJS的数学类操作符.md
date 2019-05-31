---
title: RXJS的数学类操作符
date: 2019-05-29 15:42:15
tags:
- rxjs
categories:
- 教程
---

### count： 统计数据个数

顾名思义，就是统计上游数据流所产生的数据的个数

``` javascript
var  source = of(1,2,3,4,5).pipe(
  concat(of(6,7,8))
)

source = source.pipe(
  count()
)

source.subscribe(x=>{
  console.log(x);   //8
})
```

<!-- more -->

### max和min 最大值与最小值

``` javascript
var s = of({
    name:"xbox",
    year:2001
  },
  {
    name:"xbox",
    year:2005
  },
  {
    name:"xbox",
    year:2008
  },
  {
    name:"xbox",
    year:2012
  }).pipe(
  min((a,b)=>a.year - b.year)
)

s.subscribe(x=>{
  console.log(x);  //{name: "xbox", year: 2001}
 })
```

### reduced 规约

``` javascript
class Acc{
      sum:number;
      count:number;
    }
    const source$ = range(1,3);
    const reduced$ = source$.pipe(
      reduce((acc:Acc,current:any)=><Acc>{
        sum:(acc.sum + current),
        count:(acc.count + 1)
      },{sum:0,count:0})
    )

    reduced$.subscribe(x=>{
      console.log(x); // {sum: 6, count: 3}
    })
```

如果要求平均数，只需要

``` javascript
const reduced$ = source$.pipe(
      reduce((acc:Acc,current:any)=><Acc>{
        sum:(acc.sum + current),
        count:(acc.count + 1)
      },{sum:0,count:0}),
      map(acc=>acc.sum/acc.count)
    )
    reduced$.subscribe(x=>{
      console.log(x);   //2
    })
```
