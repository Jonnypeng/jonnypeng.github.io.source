---
title: three.js的线与三角形的笔记
date: 2019-06-16 22:00:43
tags:
- three.js
categories:
- 代码片段
---

线line:

``` js
var line3 = new THREE.Line3();

      line3.start = new THREE.Vector3(0,0,0);
      line3.end = new THREE.Vector3(0,0,10);

      var center = new THREE.Vector3();
      //line3.getCenter(center);
      //center = center.addVectors(line3.start,line3.end).multiplyScalar(0.5);
      let distance = line3.distance();

      console.log(distance);//10

      //console.log(Math.sqrt(2*2 + 2*2));
      //console.log(line3.start.distanceTo(line3.end))
```
<!-- more -->

射线ray:

``` js
var p1 = new THREE.Vector3(0,5,-8);
      var p2 = new THREE.Vector3(-10,-5,-8);
      var p3 = new THREE.Vector3(10,-5,-8);
      var ray = new THREE.Ray();
      ray.origin = new THREE.Vector3(0,0,3);
      ray.direction = new THREE.Vector3(0,0,-10).normalize();  
      var result = ray.intersectTriangle(p1,p2,p3);
      console.log(result);  // {"x":0,"y":0,"z":-8}
```

三角形traiangle:

``` js
 var triangle = new THREE.Triangle();
      triangle.a = new THREE.Vector3(0,5,-8);
      triangle.b = new THREE.Vector3(-10,-5,-8);
      triangle.c = new THREE.Vector3(10,-5,-8);
      
      //var area = triangle.getArea();
      //console.log(area);  // 100
      var midpoint = new THREE.Vector3();
      midpoint = triangle.getMidpoint(midpoint);
      console.log(midpoint); //{"x":0,"y":-1.6666666666666665,"z":-8}
```