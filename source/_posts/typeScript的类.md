---
title: typeScript的类
date: 2018-11-09 22:32:30
tags:
- TypeScript
categories:
- 教程
---

private 私有修饰符，当类中的变量被设置为private时，只能在类中进行调用，而不能向外暴露

``` typescript
class Box {
  private keyValue:number;
  x:number;
  y:number;
  constructor(_x:number,_y:number){
    this.keyValue = 10;
    this.x = _x;
    this.y = _y;
  }
}

let a = new Box(1,2);

console.log(a.x);
console.log(a.keyValue); //=> error
```
<!-- more -->
protected修饰符与 private修饰符的行为很相似，但有一点不同， protected成员在派生类中仍然可以访问。例如：

``` typescript
class A {
  //private name:string;
  protected name:string;
  constructor(_name:string){
    this.name = _name;
  }
}

class B extends A{
  constructor(_name:string){
    super(_name)
  }
  public getValue(){
     return `${this.name}`;
  }
}

let jonny = new B("ss");
```

你可以使用 readonly关键字将属性设置为只读的。 只读属性必须在声明时或构造函数里被初始化。

``` typescript
class box {
  readonly blendMode:number;
  public color:number;
  constructor(_color:number){
    this.blendMode = 1;
    this.color = _color;
  }
}

let rect = new box(0xff0000);
rect.blendMode = 0;  //=>Cannot assign to 'blendMode' because it is a constant or a read-only property.
```

还可以使用存取器对类进行操作，如set与get，但是编译的时候需要注明是“ES5”，因为默认是“ES3”，如下

``` typescript
class Box {
  public color:number;

  set setColor(_color){
    this.color = _color;
  }

  get getColor():number{
    return this.color;
  }
}

let a = new Box();
a.setColor = 0xff0000;   //=> error Accessors are only available when targeting ECMAScript 5 and higher
console.log(a.getColor);
```

需要在命令行编译时候注明
``` bash
tsc index -t 'ES5'    //=>index 是文件名
```

静态成员，这些属性存在于类本身上面而不是类的实例上,静态属性的访问必须加上类的类名

``` typescript
class Box {
  static distance = {
    "x":11,
    "y":33
  }

  public z:number;

  constructor(_z:number){
    this.z = Box.distance.x + Box.distance.y + _z;
  }
}

let aBox = new Box(11);   

console.log(aBox);  //=>55
```

抽象类做为其它派生类的基类使用。 它们一般不会直接被实例化。 不同于接口，抽象类可以包含成员的实现细节。 abstract关键字是用于定义抽象类和在抽象类内部定义抽象方法。

抽象类中的抽象方法不包含具体实现并且必须在派生类中实现。 抽象方法的语法与接口方法相似。 两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含 abstract关键字并且可以包含访问修饰符。

``` typescript
abstract class Department{
  constructor(public name : string){

  }

  printName():void{
    console.log('Department name:' + this.name);
  }

  abstract printMeeting():void;
}

class AccountingDepartment extends Department {
  constructor(){
    super('Accounting and Auditing');
  }

  printMeeting():void{
    console.log('The Accounting Department meets each Monday at 10am');
  }

  generateReports():void{
    console.log("Generating accounting reports...");
  }
}

let department: Department;
//department = new Department(); //=>error Cannot create an instance of an abstract class.
department = new AccountingDepartment();
department.printName();
department.printMeeting();
department.generateReports();  //=>error  Property 'genrateReports' does not exist on type 'Department'.
```

参考文档：[官方API](https://www.tslang.cn/docs/handbook/classes.html)
