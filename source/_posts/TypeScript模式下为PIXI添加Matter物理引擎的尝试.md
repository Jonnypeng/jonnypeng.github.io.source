---
title: TypeScript模式下为PIXI添加Matter物理引擎的尝试
date: 2018-11-11 11:26:50
tags:
- PIXI.js
- Matter.js
- TypeScript
categories:
- 教程
---

TypeScript是非常适合写游戏的，原因在于游戏引擎对接口参数都有非常严格的类型要求，在原本JavaScript的模式下，对类型的“编程心理约束”实际上是非常弱的，在自己的项目经验中，记得一次写BitmapText的类型时，无意间使用了数字类型，而解释器根本不可能报错，又不可能没有编译器排错，所以导致自己花了半小时的时间找到这个错误，当然根本原因，在与，我对PIXI的BitmapText这个类本身了解程度不高，第一次使用，所以我认为如果在TypeScript模式下，在编译状态就会报错：”我传入的数据类型是Number，而类的接口是String”，废话少说，我们尝试一下在TypeScript的环境下，如何为PIXI添加物理引擎。

<!-- more -->

### 首先我们先创建DOM结构
``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <style>
  body{
    margin:0px;
  }
  #matterRender,canvas{
    position:fixed;
    width:100%;
    height:100%;
    top:0px;
    left:0px;
  }
  </style>
</head>
<body>
  <div id="matterRender"></div>    //这里是Matter的渲染器，主要是用来测试
  <canvas id="pixiRender"></canvas>   //这里是pixi的渲染器，也是上线的最终视图
  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://cdn.bootcss.com/pixi.js/4.8.2/pixi.min.js"></script>
  <script src="https://cdn.bootcss.com/matter-js/0.12.0/matter.min.js"></script>
  <script src="js/index.js"></script>
</body>
</html>
```
然后，你需要为您添加的第三方库添加typescript的声明文件，假设，你已经为你的项目作了npm init 的初始化，然后您该继续依次为这个四个文件添加声明文件
``` bash
npm install @types/jquery
npm install @types/pixi.js
npm install @types/matter-js
```

在结构设计上，我采用的是将图形渲染与物理引擎分开成两个完全独立的作用域，目的是以免变量污染，同时也利于测试状态与上线状态的切换，但是需要将物理引擎的world暴露在一个大的作用域中，目的是为了将图形的对象渲染到物理引擎中，简单看看构造

``` typescript
window.onload = init;

function init(){
  let world;   //=>将world暴露

  matter();
  pixi();

  function matter(){
    let engine = Matter.Engine.create();   
    world = engine.world;   //=>变量赋值给物理引擎

    let render = Matter.Render.create(
      <any>{
        "engine":engine,
        "element":document.getElementById("matterRender"),
        "options":{
          "width":640,
          "height":1080,
          "pixelRatio":1,
          "background":0x000000,
          "wireframes":true
        }
      });
      let runner = Matter.Runner.create(<any>{
        "fps":60
      });

      Matter.Runner.run(runner,engine);
      Matter.Engine.run(engine);
      Matter.Render.run(render);
    };

    function pixi(){

    };

  }; // init end

```

接下来，我们就开始写pixi

``` typescript
function pixi(){
    var app = new PIXI.Application(640,1080,{
      "view":<any>document.getElementById("pixiRender"),
      "transparent":true,   //=>设置为透明，这样更便于可视下一层画布
    });

    var loader = new PIXI.loaders.Loader();    //初始化加载器

    var icons = [];         //需要将DisplayObject暴露在外作用域中，便于后期同步数据

    var game = {
      "init":function (){
        loader.add(["images/bg.png","images/icon.png"]);
        loader.load(game.start);
      },
      "start":function (){
        var bg = PIXI.Sprite.fromImage("images/bg.png");
        app.stage.addChild(bg);

        game.staticRender();
        game.createIcon();
      },
      "staticRender":function (){
        /* 以下代码是设置静止物，禁止物是根据背景元素而设定的，本教程是两个矩形，并且注册点都是中心点，这是Matter默认的*/
        var rect_1 = Matter.Bodies.rectangle(206,540,20,1105,{isStatic:true,angle:(Math.PI/180)*-14.6});
        var rect_2 = Matter.Bodies.rectangle(320,903,640,20,{isStatic:true,angle:(Math.PI/180)*5});

        Matter.World.add(world,[rect_1,rect_2]);  //=>将两个矩形添加到物理引擎中
      },
      "createIcon":function (){
        class Icon extends PIXI.Sprite{
          x:number;
          y:number;
          body:any;  //这里就放置每一个DisplayObject的物理对象
          constructor(_url:string,_x:number,_y:number){
            super();
            this.texture = PIXI.Texture.fromImage(_url);
            this.x = _x;
            this.y = _y;
            this.anchor.set(0.5);    //每一个DisplayObject都必须与Matter一致，保持中心锚点
            this.body = Matter.Bodies.rectangle(this.x,this.y,100,82);   //创建物理对象，矩形且与DisplayObject保持一致
          }
        }

        for(let i = 0;i < 10;i++){
          let _icon = new Icon("images/icon.png",Math.floor(150 + Math.random()*300),i*82*-1);
          icons.push(_icon);
          app.stage.addChild(_icon);
          Matter.World.add(world,_icon.body);   //=>同理，必须将每个DisplayObject的物理对象添加到物理引擎中
        };
        game.sync();
      },
      'sync':function (){
          app.ticker.add(p2mRender);   //=> 这里是同步器，通过PIXI内置的RequestAnimationFrame方法创建监听方法
          function p2mRender(){
            icons.forEach(function (icon){
              icon.position.x = icon.body.position.x;
              icon.position.y = icon.body.position.y;
              icon.rotation = icon.body.angle;

              /*以上代码一目了然，同步显示对象与物理对象的位置与旋转*/
            });
          };
      }
    }

    game.init();

};

```

现在，就已经在TS中，为PIXI添加了物理引擎，具体的DEMO，我已经上传到GitHub中，可以下载来看看，本人很喜欢PIXI这款引擎，速度快是其一，重要的是，它并不热衷于高全方位的解决方案，而是仅仅专注渲染引擎，于是，在WEBGL2D领域，PIXI是目前最顶级的引擎，之于Phaser，哪个基于PIXI的全面解决方案，大量的工厂方法，显得好臃肿，而PIXI这种面向对象的最直接的引擎，才是可以更高度定制方案的首选引擎。

[DEMO](http://jonnypeng.com/pixi2matter/)
