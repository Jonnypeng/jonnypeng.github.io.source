---
title: Typescript的装饰器
date: 2021-01-05 10:01:30
tags:
---

#### 类装饰器

``` typescript
function age(target: Function) {
  target.prototype.sayName = function() {
    console.log("hello jonny");
  };
}

@age
class Login {
  public username: string = "";
  public password: string = "";
}

const login: any = new Login();
login.sayName(); //"hello jonny"
```

``` typescript
function setAge(param: string) {
  return function(target: any) {
    target.prototype.sayName = function() {
      console.log(param);
    };
  };
}

@setAge("Hello Candy!")
class Login {
  public username: string = "";
  public password: string = "";
}

const login: any = new Login();
login.sayName();  //'hello candy'
```

#### 属性装饰器

``` typescript
function hName(param: string) {
  return function(target: any, propertyName: string) {
    target[propertyName] = param;
  };
}

class Hello {
  @hName("Jonny")
  name: string;
}

console.log(new Hello().name); // 输出: Jonny
```

#### 方法装饰器

``` typescript
function GET(url: string) {
  return function(target, methodName: string, descriptor: PropertyDescriptor) {
    target.run = function() {
      console.log("ok");
      descriptor.value();
    };
  };
}

class HelloService {
  constructor() {}
  @GET("xx")
  getUser() {
    console.log("no");
  }
}

const instance: any = new HelloService();

instance.run();  //ok,no
```
#### 参数装饰器

``` typescript

function PathParam(paramName: string) {
  return function(target, methodName: string, paramIndex: number) {
    !target.$Meta && (target.$Meta = {});
    target.$Meta[paramIndex] = paramName;
  };
}

class HelloService {
  constructor() {}
  getUser(@PathParam("userId") userId: string) {}
}

console.log((<any>HelloService).prototype.$Meta); // {'0':'userId'}


```
