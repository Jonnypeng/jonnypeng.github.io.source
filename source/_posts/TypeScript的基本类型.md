---
title: TypeScript的基本类型
date: 2018-10-27 16:05:16
tags:
- TypeScript
categories:
- 教程
---

这两年的时间，花费了太多时间在图形绘制上，自来友为以后，由于我们这个行业的特殊性，所以前端领域在这个H5时代，主要是承接上个时代Flash的工作，所以从svg到canvas，从canvas到webgl，却较少的精力投入到DOM中，一次偶然的机会，接到一个类似APP的web项目，留个自己的时间很少，使用相对比较传统的技术完成，<!-- more -->首先配置JSON数据，然后使用php查询url地址的上get数据，通过get数据查询JSON文件，分别对不同模版进行渲染，耦合度低，修改方便，性能还过得去，但是问题来啦，微信的最新版本内置浏览器，当发现url跳转的时候，就会在页面底部侵入增加前进与后退，这就对用户体验产生极大的影响，微信这一败招从客观上推进了SPA单页面程序的演进，在我们制作Canvas画布的程序时候，一切都是SPA，也必须是SPA，一切都是JS中动态生成的，但是DOM就不同啦，10个左右的模版在1个页面渲染，1个模版对应成百上千的数据更新，要做到SPA，我找到Angular2,而不是AngularJS，因为这个是Google是推翻了上一个版本的基础上，重建的一个更新的版本，采用的是微软对JavaScript的超集语言TypeScript，抛开Angular来看，随着ES6的普及，TypeScript是一个能让您放心大胆使用ES6的语言，当然更不止于此，当程序变得庞大的时候，TypeScript有着比JavaScript更可读、可控、可维护、可扩展的优势，作为一个TypeScript的初学者，本着笨手笨脚的菜鸟精神，就数据类型写了部分DEMO，在原API文档的基础上增加了自己更清晰的表达与理解；

### boolean 布尔值
``` typescript
let status:boolean = false;       //声明一个布尔值为false
```

### number 数字
这里与JavaScript的区别在于，JavaScript在编写number的时候，只能采用十进制与十六进制的表示法，而同为number，TypeScript可以使用二进制与八进制
``` typescript
let decLiteral: number = 6;       //十进制
let hexLiteral: number = 0xf00d;    //十六进制
let binaryLiteral: number = 0b1010;   //二进制
let octalLiteral: number = 0o744;       //八进制
```

### string 字符串
TS的字符串连接，使用\`\`全部语句包起来，里面的变量则使用${}，{}内可以直接使用表达式
``` typescript
let game:string = "watch dogs";
let company:string = "ubisoft";

let store = game + company;    //=> 与JS一样的字符串连接
let message = `${game}由${company}出品，价格为${price},打折为${price * 0.6}`;   //=> TS独有的字符串连接
```

### array 数组
``` typescript
let listA:number[] = [1,2,3,4,5];    //声明为数字的数组
let listB:Array<any> = ["jonny",1,false];     //声明为泛型的数组

listA[5] = "sony";   //报错，类型错误
listA[5] = 30;   //正确
listA[10] = 20;   //正确

listB[3] = function (){   //正确
let a = "hello";
};
```

### Tuple 元组
当编译为js后，元祖就是数组
``` typescript
let x:[string,number];   //声明顺序也是赋值顺序
x = [10,"hello"];   //返回错误,因为赋值类型的顺序必须与声明时候一致
x = ["hello",10];  //返回正确
x[3] = 23;    //赋值失败，因为长度为2
```

### Enum 枚举
enum类型是对JavaScript标准数据类型的一个补充，可以这样理解，枚举的每一个值，默认都是number，每个值都是前一个值的递增1后的值，当用户定义了其中一个值后，随后的值会以此递增，譬如默认是0,1,2,3,4,5,6....,当你将4修改为20的时候，就变为0,1,2,3,20,21,22...,而枚举可以为这些数字添加可读的属性名称，譬如0叫PS4，1叫XBOX，2叫MAC，根据程序员自己定义,如果对枚举类型还不够理解，注意看下面的DEMO，相信一定能够理解
``` typescript
enum Week {Sun,Mon,Tues,Wed,Thurs,Fri,Sat};    //=>0,1,2,3,4,5,6 为从0开始递增的值进行编号，分别为Sun...
let a:Week = Week.Mon;
let e:Week = Week.Fri;
console.log(a,e);   //=>1,5

enum Floor {Switch = 20,PlayStion,Xbox,Google,Amazon = 40,Sony} //=>20,21,22,23...递增40..41
let elevatorA:Floor = Floor.Google;
let elevatorB:Floor = Floor.Amazon;
let elevatorC:Floor = Floor.Sony;
console.log(elevatorA,elevatorB,elevatorC);     //=>23,24,41

enum Game {FarCary,WatchDogs,BattleMen = "end",Hol}   //=>报错，当一个枚举属性被赋值为字符串的时候，将终止这个枚举，往后添加属性为报错
enum Game {FarCary,WatchDogs,BattleMen = "end"};
let game:Game = Game.BattleMen;
console.log(game);      //=> end
```

### Any 泛型
一个让你激动的类型，一个既可以给你造人、造鸡、造鸭，还能给这些动物赋予方法的类型，当然你还可以让他们一起大合唱；
``` typescript
let name:any = 0;
name = "Jonny";
name = false;
name = {"uid":1};
console.log(name);   //=>okay;

let playerA:any = {
  "name":"xbox",
  "play":function (){
    return `Hello ${this.name}`;
  }
}

console.log(playerA.play());   //=>hello xbox
```

### Void 没有返回值
声明一个void类型的变量只能为它赋予undefined和null
``` typescript
function a(){
  let a:number = 1;
}

//console.log(a());   //=>undefined

function b():void{
  //let a:number = 2;
  console.log("This is my message");
};

b();   //=>void类型像是与any类型相反，它表示没有任何类型。 当一个函数没有返回值时，你通常会见到其返回值类型是 void

let onkyo:void = "yes";   //error
let onkyo:void = undefined;  //okay
let pioneer:void = null;     //okay

```

### Null and Undefined 空与未定义
``` typescript
let a:null = null;
let b:undefined = undefined;
let c:number = null;    //在没有--strictNullChecks标记的情况下，null和undefined赋值给number类型的变量
```

### Never 永不存在的值
官方注明叫永不存在的值，当你在一个函数中注明返回值为never的时候，说明你期望返回的是一个永不存在的值，而不是一个不会返回值void，返回never这个状态只会出现在抛出异常、没有返回值的函数表达式、箭头函数表达式的返回值类型中,而且never永不为真。

never 是所有类型的子类型,虽然我声明了这个函数的返回值为number，但是返回的是never，因为任何类型的返回值，都可以是never

``` typescript
function a():number{
  throw new Error("听说你是泛型，我是never，我来找你");   //=> okay
};
```

以下场景是声明为never
```typescript
function b():never{
  throw new Error("this is error");
};

function c(message:string):never{
  while(true){
    console.log("never");
  }
}
```

### Object
官方定义：“object表示非原始类型，也就是除number，string，boolean，symbol，null或undefined之外的类型。”
``` typescript
function ok():object{
  let a = {"name":"jonny"};
  let b = 20;
  return a;   //=> okay ，返回了object
  return b;   //=> error, 返回的是数字
};

let a = {"name":"jonny","say":function (){
  return `Hello ${this.name}`;
}};

console.log(a.say());   //=> okay 按照js的object的习惯进行方法调用
console.log(a.think());   //=> okay 按照js的object的习惯进行方法调用

```
以下的例子，我需要比较object与any的区别，这主要体现在编译状态下

``` typescript
let a = {"name":"jonny","say":function (){
  return `Hello ${this.name}`;
}};

console.log(a.think());   //=> 编译报错，因为object没有指定think这个属性

let b:any = a;   //=> 重定义一个变量b，声明为any，并将a:object赋值给b:any
console.log(b.say());    // =>okay
console.log(b.think());   // ==> okay,尽管b没有think这个属性，但是编译也是成功的
```
上面的例子说明any可以是任何类型，而object调用未声明的属性则为编译报错，但是当执行为js后，b:any依然会报错，报错信息为b.think不是一个函数

### Type assertions 类型断言
这是一个类型是TypeScript认为你比它自己更明白这个变量是什么类型，因为这个可能是无效的，但是程序员告诉编译器，我知道这是什么，你给我编译就是啦!

``` typescript
let anim:string = "cat";
let animLengthA:number = (<string>anim).forEach;  //=>与下一个as语法是等价
let animLengthB:number = (anim as string).length;    //＝> jsx语法中，只认可这个as语法;
```

当我不使用类型断言，编译以下代码
``` typescript
let anim:any = "cat";
let anl:number = anim.length;  //=> okay 也是成功的,因为它本身是正确的
```

下面是一个非常有用的例子，可以解释类型断言之于程序员，是与程序本身在编译过程中对话的过程，并更正了程序编译过程中僵硬错误；
情景是：在开发过程中，后端传给我的数据有可能是string或者number，但是我需要返回的是length的属性，而number却没有这个属性，于是我这样编写代码

``` typescript
function getPhone( phone:string | number ):number{   //=>在测试过程中，后端传给我的数据有可能是string或者number，

  if(phone.length){
    return phone.length;  //=>如果是 string ，直接输出length；
  }else{
    return phone.toString().length;   //=> 如果是number，则专为string后输出length
  }
};

getPhone(18984890000);  //=> err,报错说， string | number 这个联合类型没有length这个属性

```
这个时候，我将phone修改为(<string>phone),我告诉程序，说这个if语句上的phone的类型是string，然后编译就成功了，如下：

``` typescript
function getPhone( phone:string | number ):number{   

  if((<string>phone).length){     
    return (<string>phone).length;  
  }else{
    return phone.toString().length;   
  }
};

getPhone(18984890000);  //=> okay
```

终于基本上完成了TypeScript的基本类型这篇文章，写博客是一件很费时间的事情，但是也获得了很大的快乐，快乐在于整理自己的思路，使得自己的知识更具条理，同时写作能够放大时间，能够在某一个时间轴上投入很大的精力去细细的琢磨那些看上去没有什么用的东西，但我反而认为越看上去无聊的、意义不大的细节，才是一门语言的伟大之处，也许我认为的伟大并不在于宏大的叙事，而在于人们很难觉察的细节也能绽放创造者投入的智慧与精力；

很喜欢TypeScript，并向TS的创造团队及C#之父'安德斯·海尔斯伯格'致敬,'安德斯·海尔斯伯格'没有高学历，也不是计算机信息科系出身，凭着自己的天赋与对编程的热爱，他创造Delphi、C#和TypeScript与.NET，像我之类，在这个强者如林的社会，没有学历的野生程序员，有一份养家糊口的工作，就已经心满意足，凭着自己对编程的热爱，以及自己的笨手笨脚般的努力，相信自己还能走得更远！

*参考文档
[TypeScript 官方API 基础类型](https://www.tslang.cn/docs/handbook/basic-types.html)
[TypeScript 入门教程](https://ts.xcatliu.com/basics/type-assertion.html)*
