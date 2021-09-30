---
title: ts的类型系统
date: 2021-09-25 14:55:48
tags:
- typescript
---

### 接口

TypeScript的核心原则之一是对值所具有的_结构_进行类型检查。 它有时被称做“鸭式辨型法”或“结构性子类型化”。 在 TypeScript 里，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约。

<!--more-->

下面通过一个简单示例来观察接口是如何工作的：

``` ts
function printLabel(labeledObj: { label: string }) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);

```

类型检查器会查看`printLabel`的调用。 `printLabel`有一个参数，并要求这个对象参数有一个名为`label`类型为`string`的属性。 需要注意的是，我们传入的对象参数实际上会包含很多属性，但是编译器只会检查那些必需的属性是否存在，并且其类型是否匹配。 然而，有些时候 TypeScript 却并不会这么宽松，我们下面会稍做讲解。

下面我们重写上面的例子，这次使用接口来描述：必须包含一个`label`属性且类型为`string`：

``` ts
interface LabeledValue {
  label: string;
}

function HerprintLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let HerObj = { size: 10, label: "Size 10 Object" };
HerprintLabel(myObj);
```

`LabeledValue`接口就好比一个名字，用来描述上面例子里的要求。 它代表了有一个`label`属性且类型为`string`的对象。 需要注意的是，我们在这里并不能像在其它语言里一样，说传给`printLabel`的对象实现了这个接口。我们只会去关注值的外形。 只要传入的对象满足上面提到的必要条件，那么它就是被允许的。

还有一点值得提的是，类型检查器不会去检查属性的顺序，只要相应的属性存在并且类型也是对的就可以。


### 可选属性
 
接口里的属性不全都是必需的。 有些是只在某些条件下存在，或者根本不存在。 可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象中只有部分属性赋值了。

下面是应用了“option bags”的例子：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createMiniSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = { color: "white", area: 100 };
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createMiniSquare({ color: "black" });
```

带有可选属性的接口与普通的接口定义差不多，只是在可选属性名字定义的后面加一个`?`符号。

可选属性的好处之一是可以对可能存在的属性进行预定义，好处之二是可以捕获引用了不存在的属性时的错误。 比如，我们故意将`createSquare`里的`color`属性名拼错，就会得到一个错误提示：

### 只读属性

一些对象属性只能在对象刚刚创建的时候修改其值。 你可以在属性名前用`readonly`来指定只读属性:

```ts

interface Point {
  readonly x: number;
  readonly y: number;
  z: number;
}

// 你可以通过赋值一个对象字面量来构造一个`Point`。 赋值后，`x`和`y`再也不能被改变了。

let p1: Point = { x: 10, y: 20,z:30 };
p1.x = 5; // error! Cannot assign to 'x' because it is a read-only property
p1.z = 9;

```

通过泛型操作实现自渎全继承

```ts
const xbox: Readonly<Point> = {
  x:0,
  y:0,
  z:0
}

xbox.z = 30; //error Cannot assign to 'z' because it is a read-only property.
```

TypeScript 具有`ReadonlyArray<T>`类型，它与`Array<T>`相似，只是把所有可变方法去掉了，因此可以确保数组创建后再也不能被修改：

``` ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error! 思考 为什么?? 
```

当引用类型被声明为只读时，不仅不能重新赋值改变内存地址，同时不能通过属性操作符方式改变该内存下的数据,上面代码的最后一行，可以看到就算把整个`ReadonlyArray`赋值到一个普通数组也是不可以的。 但是你可以用类型断言重写：

``` ts
a = ro as number[];// `readonly` vs `const`
```

最简单判断该用`readonly`还是`const`的方法是看要把它做为变量使用还是做为一个属性。 做为变量使用的话用`const`，若做为属性则使用`readonly`。


### 函数类型 

接口能够描述 JavaScript 中对象拥有的各种各样的外形, 除了描述带有属性的普通对象外，接口也可以描述函数类型。为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。

``` ts
interface OldSearchFunc {
  chekcout (source: string, subString: string):boolean;
  show (show: boolean): number;
  say( sayFuc: (yourName: string) => void): void;
}

// 这样定义后，我们可以像使用其它接口一样使用这个函数类型的接口。 下例展示了如何创建一个函数类型的变量，并将一个同类型的函数赋值给这个变量。

let mySearch: OldSearchFunc;
mySearch.chekcout = function(source: string, subString: string) {
  let result = source.search(subString);
  return result > -1;
};
mySearch.show = (show: boolean) => {
  if(show){
    return 1;
  }
  return 0;
}
mySearch.say = (sayFuc: (yourName: string) => void) => {
  let myName = "hello";
  sayFuc(myName);
}
mySearch.say((yourName: string)=>{console.log(yourName)});
```

对于函数类型的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。 比如，我们使用下面的代码重写上面的例子：

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let mySearch1: SearchFunc;
mySearch1 = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  return result > -1;
};
const result:boolean = mySearch1('abc','efg');
```

### 可索引的类型

 与使用接口描述函数类型差不多，我们也可以描述那些能够“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`。 可索引类型具有一个_索引签名_，它描述了对象索引的类型，还有相应的索引返回值类型。 让我们看一个例子：

``` ts
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];

let myStr: string = myArray[0];
```

上面例子里，我们定义了`StringArray`接口，它具有索引签名。 这个索引签名表示了当用`number`去索引`StringArray`时会得到`string`类型的返回值。

Typescript 支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。 这是因为当使用`number`来索引时，JavaScript 会将它转换成`string`然后再去索引对象。 也就是说用`100`（一个`number`）去索引等同于使用`"100"`（一个`string`）去索引，因此两者需要保持一致。

``` ts
class Animal {
   name: string;
}
class Dog extends Animal {
  breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
  [x: string]: Animal;
  [y: number]: Dog;
}
```

字符串索引签名能够很好的描述`dictionary`模式，并且它们也会确保所有属性与其返回值类型相匹配。 因为字符串索引声明了`obj.property`和`obj["property"]`两种形式都可以。 下面的例子里，`name`的类型与字符串索引类型不匹配，所以类型检查器给出一个错误提示：

```ts
interface NumberDictionary {
  [index: string]: number;
  length: number; // 可以，length是number类型
  name: string; // 错误，`name`的类型与索引类型返回值的类型不匹配
}
``

但如果索引签名是包含属性类型的联合类型，那么使用不同类型的属性就是允许的。

```ts
interface NumberOrStringDictionary {
   [index: string]: number | string;
   length: number;    // ok, length is a number
   name: string;      // ok, name is a string
}
```

最后，你可以将索引签名设置为只读，这样就防止了给索引赋值：

```ts
interface ReadonlyStringArray {
  readonly [index: number]: string;
}
let myArray2: ReadonlyStringArray = ["Alice", "Bob"];
myArray2[2] = "Mallory"; // error!
// 你不能设置`myArray[2]`，因为索引签名是只读的。
```
### 实现接口

与 C\#或 Java 里接口的基本作用一样，TypeScript 也能够用它来明确的强制一个类去符合某种契约。

``` ts
interface OldClockInterface {
  currentTime: Date;
}

class Clock1 implements OldClockInterface {
  currentTime: Date = new Date();
  constructor(h: number, m: number) {}
}
```

你也可以在接口中描述一个方法，在类里实现它，如同下面的`setTime`方法一样：

```ts
interface NewClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock2 implements NewClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```

这里因为当一个类实现了一个接口时，只对其实例部分进行类型检查。 constructor 存在于类的静态部分，所以不在检查的范围内。

 因此，我们应该直接操作类的静态部分。 看下面的例子，我们定义了两个接口，`ClockConstructor`为构造函数所用和`ClockInterface`为实例方法所用。 为了方便我们定义一个构造函数`createClock`，它用传入的类型创建实例。

```ts
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
  tick(): void;
}

function createClock(
  ctor: ClockConstructor,
  hour: number,
  minute: number
): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
}
class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("tick tock");
  }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

因为`createClock`的第一个参数是`ClockConstructor`类型，在`createClock(AnalogClock, 7, 32)`里，会检查`AnalogClock`是否符合构造函数签名。

另一种简单方式是使用类表达式：

```ts
interface ClockConstructor {
  new (hour: number, minute: number);
}

interface ClockInterface {
  tick();
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
};
```

### 继承接口

和类一样，接口也可以相互继承。 这让我们能够从一个接口里复制成员到另一个接口里，可以更灵活地将接口分割到可重用的模块里。

```ts
interface Shape {
  color: string;
}

interface Square extends Shape {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口，创建出多个接口的合成接口。

```ts
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let newSquare = {} as Square;
newSquare.color = "blue";
newSquare.sideLength = 10;
newSquare.penWidth = 5.0;
```

### 混合类型

 先前我们提过，接口能够描述 JavaScript 里丰富的类型。 因为 JavaScript 其动态灵活的特点，有时你会希望一个对象可以同时具有上面提到的多种类型。

 一个例子就是，一个对象可以同时做为函数和对象使用，并带有额外的属性。

```ts
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = function(start: number) {} as Counter;
  counter.interval = 123;
  counter.reset = function() {};
  return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

在使用 JavaScript 第三方库的时候，你可能需要像上面那样去完整地定义类型。


### 接口继承类


 当接口继承了一个类类型时，它会继承类的成员但不包括其实现。 就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。 接口同样会继承到类的 private 和 protected 成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。

当你有一个庞大的继承结构时这很有用，但要指出的是你的代码只在子类拥有特定属性时起作用。 除了继承自基类，子类之间不必相关联。 例：

```ts
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control implements SelectableControl {
  select() {}
}

class TextBox extends Control {
  select() {}
}

class ImageControl implements SelectableControl {
// Error: Class 'ImageControl' incorrectly implements interface 'SelectableControl'.
//  Types have separate declarations of a private property 'state'.
  private state: any;
  select() {}
}
```

在上面的例子里，`SelectableControl`包含了`Control`的所有成员，包括私有成员`state`。 因为`state`是私有成员，所以只能够是`Control`的子类们才能实现`SelectableControl`接口。 因为只有`Control`的子类才能够拥有一个声明于`Control`的私有成员`state`，这对私有成员的兼容性是必需的。

在`Control`类内部，是允许通过`SelectableControl`的实例来访问私有成员`state`的。 实际上，`SelectableControl`就像`Control`一样，并拥有一个`select`方法。 `Button`和`TextBox`类是`SelectableControl`的子类（因为它们都继承自`Control`并有`select`方法）。而对于 `ImageControl` 类，它有自身的私有成员 `state` 而不是通过继承 `Control` 得来的，所以它不可以实现 `SelectableControl` 。

### typeof 映射类型

```ts
const myBox = {
  x:1,
  y:2,
  z:3,
  color:"#fff"
};

type Box = typeof myBox;
type BoxValue = typeof myBox['x'|'y'|'z'];

const myArr = [10,9,8,7,"x",true] as const;
type MyArrValue = typeof myArr[number];
```

### keyof 映射键

```ts
type BoxKey = keyof typeof myBox;
```

 Partial<Type> 构造一个 Type 的所有属性都设置为 optional 的类型。此实用程序将返回表示给定类型的所有子集的类型

```ts
 interface Todo1 {
  title: string;
  description: string;
}

type OptionTodo = Partial<Todo1>;
```

Required<Type> 构造一个由 Type 设置为 required 的所有属性组成的类型。与Partial相反
 
```ts
 interface Todo2 {
  title?: string;
  description?: string;
}

type RequiredTodo = Required<Todo2>;
```


### Readonly<Type>

```ts
type ReadonlyTodo = Readonly<Todo2>;

const readonlyTodoIm: ReadonlyTodo = {
  description:'ss'
}

readonlyTodoIm.title = 'abc'; // err Cannot assign to 'title' because it is a read-only property
```


### Record<Keys,Type>

```ts
 interface CatInfo {
  age: number;
  breed: string;
}

type CatName = "miffy" | "boris" | "mordred";

type Cat = Record<CatName,CatInfo>;
```

### Pick<Type, Keys> 

从一个接口中以字符串key复制类型到新类型中

```ts
 interface Todo3 {
  title: string;
  description: string;
  completed: boolean;
}

type TodoPreview = Pick<Todo3, "title" | "completed">;

const todo: TodoPreview = {
  title: "Clean room",
  completed: false,
};


### Omit<Type, Keys> 

从一个接口中以字符串key删除相应的类型,产生新的类型

```ts
interface AppInfo{
  name: string;
  devName: string;
  phone: string;
}

type SimAppInfo = Omit<AppInfo,"phone">;

const app: SimAppInfo = {
  name:'abc',
  devName:'ef'
}
```

### Exclude<Type, ExcludedUnion> 

在原来的接口中排除非唯一标识符字符串后得到的 只包含唯一标识符的类型

```ts
 type T0 = Exclude<"a" | "b" | "c", "a">;
 type T1 = Exclude<"a" | "b" | "c", "a" | "b">;
 type T2 = Exclude<string | number | (() => void), Function>;
```

### extends of

泛型中映射键的子集

```ts
function getValue<T,K extends keyof T>(obj:T,key: K):T[K]{
  return obj[key];
}

getValue({name:'jonny'},'name');
```



### in 操作符

映射所有key属性，而不是子集

```ts
type Option<T> = {
  [K in keyof T]?: T;
}

interface Log{
  name:'jonny',
  age: 10
}

type OptionLog = Option<Log>;
```