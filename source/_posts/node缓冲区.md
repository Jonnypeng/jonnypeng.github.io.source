---
title: node缓冲区
date: 2019-02-01 11:29:53
tags:
- node
- 缓冲区
categories:
- 教程
---
JavaScript 语言自身只有字符串数据类型，没有二进制数据类型。

但在处理像TCP流或文件流时，必须使用到二进制数据。因此在 Node.js中，定义了一个 Buffer 类，该类用来创建一个专门存放二进制数据的缓存区。

在 Node.js 中，Buffer 类是随 Node 内核一起发布的核心库。Buffer 库为 Node.js 带来了一种存储原始数据的方法，可以让 Node.js 处理二进制数据，每当需要在 Node.js 中处理I/O操作中移动的数据时，就有可能使用 Buffer 库。原始数据存储在 Buffer 类的实例中。一个 Buffer 类似于一个整数数组，但它对应于 V8 堆内存之外的一块原始内存。

*在v6.0之前创建Buffer对象直接使用new Buffer()构造函数来创建对象实例，但是Buffer对内存的权限操作相比很大，可以直接捕获一些敏感信息，所以在v6.0以后，官方文档里面建议使用 Buffer.from() 接口去创建Buffer对象*

<!-- more  -->
``` javascript
/* 缓冲区分配 */
/*
const Buffer: {
    new (str: string, encoding?: string): Buffer;
    new (size: number): Buffer;
    new (array: Uint8Array): Buffer;
    new (arrayBuffer: ArrayBuffer | SharedArrayBuffer): Buffer;
    new (array: any[]): Buffer;
    new (buffer: Buffer): Buffer;
    ... 11 more ...;
    poolSize: number;
}
*/
const buf1 = Buffer.alloc(10);   // <Buffer 00 00 00 00 00 00 00 00 00 00>
const buf2 = Buffer.alloc(10,12);   // <Buffer 0c 0c 0c 0c 0c 0c 0c 0c 0c 0c>
const buf3 = Buffer.allocUnsafe(10); //非安全、非填充式的分配，可能会包含敏感区域<Buffer 68 7b 04 03 01 00 00 00 80 7b>

/* 构建一个缓冲区，并同时创建缓冲区的数据 */
/*
const Buffer: {
    new (str: string, encoding?: string): Buffer;
    new (size: number): Buffer;
    new (array: Uint8Array): Buffer;
    new (arrayBuffer: ArrayBuffer | SharedArrayBuffer): Buffer;
    new (array: any[]): Buffer;
    new (buffer: Buffer): Buffer;
    ... 11 more ...;
    poolSize: number;
*/
const buf4 = Buffer.from([1,2,3,4]);  //<Buffer 01 02 03 04>
const buf5 = Buffer.from("ABC","utf8");   //<Buffer 41 42 43>

/* 缓冲区写入 */
/* 
(method) Buffer.write(string: string, offset?: number, length?: number, encoding?: string): number
*/
var input_1 =  buf1.write("HelloWorld",0,10,"utf8")   //你已经写入了10
buf2.write("Jonny",0,5,'utf8');
var out = buf1.toString("utf8",0,9);   //HelloWorl 

/* 缓冲区转换为字符串与JSON */
/* 
    (method) Buffer.toJSON(): {
    type: "Buffer";
    data: any[];
}
*/
/*
    (method) Buffer.toString(encoding?: string, start?: number, end?: number): string
*/

var out1 = buf1.toJSON();    //{type: 'Buffer', data: [ 72, 101, 108, 108, 111, 87, 111, 114, 108, 100 ] }
var str = buf2.toString('utf8',0,6);

/* 缓冲区合并 */
/* (method) concat(list: Uint8Array[], totalLength?: number): Buffer */
var totalBuffer = Buffer.concat([buf1,buf2],10);   //HelloWorld

/* 缓冲区比较 */
/*(method) Buffer.compare(otherBuffer: Uint8Array, targetStart?: number, targetEnd?: number, sourceStart?: number, sourceEnd?: number): number */
var buf6 = Buffer.from("ABC");
var buf7 = Buffer.from("DEF");
var buf8 = Buffer.from("ABC");

var result1 = buf6.compare(buf7);   // result1 < 1;表示buf6在buf7之前
var result2 = buf7.compare(buf6);   // result2 > 1;表示buf7在buf6之后
var result3 = buf8.compare(buf6);   // result3 = 0,表示buf8与buf6相同

/* 拷贝缓冲区 */
/*(method) Buffer.copy(targetBuffer: Uint8Array, targetStart?: number, sourceStart?: number, sourceEnd?: number): number */

buf6.copy(buf7,1,0,2);  
var result4 = buf7.toString();  // 将buf6的AB 拷贝到 buf7 的 1 位之后，buf7变成DAB


/* 缓冲区裁剪 */
// (method) Buffer.slice(start?: number, end?: number): Buffer    这个end准确地说是length，而不是索引位置
var buf9 = buf8.slice(0,2);   // 返回一个新的缓冲区，但是它和旧的缓冲区指向同一个内存，并按照slice定义的方法进行裁剪
	
```
