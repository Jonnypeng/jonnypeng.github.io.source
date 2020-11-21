---
title: JS状态模式
date: 2020-11-21 12:52:22
tags:
---
将每个状态封装成类，通过场景进行映射相关的状态类，从而实现状态模式

下面制作一个关于灯光的场景，灯光按钮有三种状态，按每一个按钮，都会触发一个信号，并把下一个状态传递给下一个类

<!-- more · -->

``` ts
// 这是灯光状态的抽象类
abstract class LightState{
    abstract state:string;
    light:Light;

    constructor(light:Light){
        this.light = light;
    }

    print(){
        console.log(this.state);
    }

    abstract changeState():void;
}

//关灯
class OffLightState extends LightState{
    state:string = 'off';
    constructor(light:Light){
        super(light);
    }
    changeState(){
        this.light.setState(this.light.weakState);
    }
}

//暖灯
class WeakLightState extends LightState{
    state:string = 'weak';
    constructor(light:Light){
        super(light);
    }
    changeState(){
        this.light.setState(this.light.strongState);
    }
}

//强灯
class StrongLightState extends LightState{
    state:string = 'strong';
    constructor(light:Light){
        super(light);
    }
    changeState(){
        this.light.setState(this.light.offLightState);
    }
}

class Light{
    offLightState:OffLightState = new OffLightState(this);
    weakState:WeakLightState = new WeakLightState(this);
    strongState:StrongLightState = new StrongLightState(this);
    currentState:LightState = this.offLightState;

    setState(newState:LightState){
        this.currentState = newState;
    }

    //点击事件
    onPress(){
        this.currentState.print();
        this.currentState.changeState();
    }
}

let light = new Light();

light.onPress();//off
light.onPress();//weak
light.onPress();//strong
```