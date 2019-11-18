---
title: phaser的魔性
date: 2019-11-18 15:22:54
tags:
- phaser
---

首先，不是在数落某个引擎不好，但不代表所有的事物都是完美的，phaser的V3版本去除了PIXI渲染引擎，更新频率有点大，也在完善之中，phaser的编程风格是比较偏于过程化的，但是并不代表不可以按照面向对象的风格去进行，但是会添加额外的代码，我仍然认为，面向对象依然是维护一套方案最好的编程风格；

#### container中的sprite无法更新动画

<!-- more -->

我想你会这样写:

```js
let cubeAnimation = this.anims.create({
            key: 'spin',
            frames: this.anims.generateFrameNames('cube', { prefix: 'frame', start: 0, end: 23 }),
            frameRate: 50,
            repeat: -1
        });
let layer = this.add.container(0,0);
let cube = new Phaser.GameObjects.Sprite(this,400,300,'cube');
layer.add(cube);
cube.play('spin');
```

如果使用构造函数的方法添加到容器中，是不能更新sprite的animation；除非你这样写

```js
let layer = this.add.container(0,0);
let cube = this.add.sprite(400, 300, "cube");
layer.add(cube);
cube.play('spin');
```
或

```js
let layer = this.add.container(0,0);
let cube = new Phaser.GameObjects.Sprite(this,400,300,'cube');
this.add.existing(cube);
layer.add(cube);
cube.play('spin');
```

也就是说，使用sprite的时候，必须先增加到scene中，然后再增加到container中，才能更新animation，添加到scene中后又添加到container中，会自动在scene的list中删除这个sprite，放置到container的list中，所以不用疑虑；

#### mask 没有localPosition

首先mask如果是从geometry中生成的，那么这geometry的Graphics类必须是在scene使用make创建，也就是只是注册到游戏列表中，但是不能添加到scene中，同时也不能添加到container中，mask只能是在worldPosition中，而不能具有localPosition，所以既不能在scene或container的list中，只能是抽象的存在

``` js
        let layer = this.add.container(0, 0);
        let truck = new Phaser.GameObjects.Image(this, 0, 0, 'truck').setPosition(0, 300);
        let shape = this.make.graphics().fillStyle(0xffffff).fillCircle(400, 300, 200);
        let shapeMask = shape.createGeometryMask();
        truck.setMask(shapeMask);   // =>ok

        // layer.add(truck);
        // layer.setMask(shapeMask); // =>ok
        // truck.setMask(shapeMask);   // =>ok
```

以上写法都是可以运行的