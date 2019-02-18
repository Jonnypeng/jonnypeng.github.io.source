---
title: TypeScript的接口
date: 2018-10-29 18:06:48
tags:
- TypeScript
- interface
categories:
- 教程
---

接口定义的是一个属性的类型，声明了一个接口名称，以及的属性:类型;
调用接口时，会对传入的参数，比对接口的属性进行同类检查，不按顺序地检查，同类型则通过编译

<!-- more -->
### 常规模式

``` typescript
interface priceValue {    
  price : number
}


function getPrice(_price:priceValue){  
  return _price;
};

getPrice({price:100});  // okay 全部符合
getPrice({price:"100"});   //=> error ,类型不符合,声明传入的接口类型中的“price”为”number“，而传入的是string类型
getPrice({name:"xbox"});   //=>error, 属性不符合，没有传入接口指定的属性
```
### 只读属性

``` typescript
interface position {    //=> 定义一个三维坐标
  x:number,
  y:number,
  readonly z:number
}

let boyPosition : position = {
  x:320,
  y:540,
  z:0
}

boyPosition.x = 0;   //ok
boyPosition.z = 100;   //=>编译器判断这是一个const或者只读属性，不能修改
```

以下是数组类型的只读约束

``` typescript
let star:ReadonlyArray<string> = ["Jonny","Sonic","HMV"];

///star[0] = "xbox";  //=>error;
///star.push("ps4");   //=>error;
// star.length = 4;    //=>error;
/*以上代码全部错误，因为star是只读数组，任何赋值与修改，都是错误的*/

//let sky:Array<string> = star;     //=> error,甚至被赋值给其它变量都是不允许的
let sky:Array<string> = star as Array<string>;   //=> okay,只有对只读数组在被赋值的同时，进行类型断言才会被通过
```

### 可选模式

```typescript
interface list{
  A:number;
  B?:number;    //定义一个可选的属性及类型，只有存在的时候，才会生效
}

function getList(_list:list){
  return _list;
};

getList({   //=> ok,全部符合
  A:100,
  B:200
});

getList({    //=>ok,没有B属性，但是B属性是可选
  A:100
});

getList({     //=>error,传入了一个为定义的属性
  A:100,
  C:200
});

```

### 索引签名

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;    //除以上类型以外的所有类型都可以
}

function createSquare(config: SquareConfig){
  // ...
}


let mySquare = createSquare({ colour: "red", width: 100 })  //=>ok

```

### 函数的接口

函数的参数名不需要与接口里定义的名字相匹配，如下面的例子

``` typescript
interface position {
  (x1:number,x2:number):boolean;  //定义一个参数列表并注明返回值的类型
}

let comparison:position; //声明一个变量，并且定义这个变量的接口

comparison = function (aX,bX){   //变量的函数表达式，参数列表按照position进行定义
  if(aX > bX){
  return true;        //返回值必须按照position的返回值类型进行定义
}else{
  return false;
}
};

let result = comparison(20,100);   //=> false
```

当传递参数的顺序与接口定义的参数的顺序不同的时候,就会报错
``` typescript
interface box {
  (width:number,text:string):string;
}

let xbox:box;

xbox = function (_width,_text){
  return `${_text}的边长为${_width}`
};

console.log(xbox(30,"watchDog"));   //ok;
console.log(xbox("watchDog",30));  //error;报错，首先就说传入的参数不是接口指定的number类型

```

### 索引签名


``` typescript
interface name {
  [index:number]:string;      //索引被指定为number，而索引下的返回值则为string
}

let nameList:name;

nameList = ["Jonny","Candy","Daituo","Lilei"];

console.log(nameList[1]);   //=>  Candy；

nameList[4] = 20;  //=> error 返回值不是字符串
nameList["age"] = "string";   //=> ok ,虽然索引值在接口中定义为了number，但是如果设置为string，也不会报错，因为JavaScript在执行程序的时候，就算你传递的是number，也会被转成string
```

当索引值类型定义为number，实际也会转为string;
TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。 这是因为当使用 number来索引时，JavaScript会将它转换成string然后再去索引对象。 也就是说用 100（一个number）去索引等同于使用"100"（一个string）去索引，因此两者需要保持一致。

```typescript
class Animal {
  name: string;
}
class Dog extends Animal {
  breed: string;
}

// 错误：
interface NotOkay {
  [x: number]: Animal;  //=>error； Numeric index type 'Animal' is not assignable to string index type 'Dog'.
  [x: string]: Animal;
}
```
