---
title: ES6 - ch17 Generator 函数的异步应用
date: 2018-06-11 22:00
categories: blog
tags: [book, js]
---

受限于Javascript的“单线程”执行环境，如果没有异步编程， 程序会卡死无法使用。

ES6诞生以前， 异步编程的方法大概有下面4种。
* 回调函数
* 事件监听
* 发布/ 订阅
* Promise对象

### 发电机/发生器/母线
问谷歌的话 Generator 的字面意思是“发电机”，第二个意思是发生器，到这儿就差不多了

Generator：a thing that generates something, in particular.

既然是发生器靠什么发生呢？是yield,那么什么是particular呢？就是yield的第二个意思见下文。

```js
//generator
//定义前的*表示生成器函数的与众不同
function* gen(x){
    var y = yield x+2;
    return y;
}
```
yield：产生, 屈服, 产, 让步, 退让

从程序语义上yield既有产生又有让步的意思，先是把后面的表达式结果生产出去然后保留状态后退出。

这个可能就是学习二手知识不可避免的坑吧，从英文原版的字面意思就是既有生产又有退出的双重意思😓。
翻译过来就剩下生产了。

```js
var g = gen(1);
console.log(g.next());
console.log(g.next(2));

bblu@ThinkPad:~/repo/nodejs/practice$ node generator.js
{ value: 3, done: false }
{ value: 2, done: true }
/*
第二个 next 方法带有参数2，这个参数可以传入 Generator 函数，
作为上个阶段异步任务的返回结果，被函数体内的变量 y 接收。
因此，这一步的 value 属性，返回的就是2（变量 y 的值）。

怎么理解Generator参数被作为上阶段的返回结果？
就是重新指派了上一次yield表达式的返回值.
*/

function * genNum(x){
    var y = yield x + 1;
    var z = yield y + 1;
    return z;
}

g = genNum(0);
console.log(g.next());
console.log(g.next(2));
console.log(g.next(3));
console.log(g.next());

/*
[macbook]:practice bblu$ node generator.js 
{ value: 1, done: false }
{ value: 3, done: false }
{ value: 3, done: true }
{ value: undefined, done: true }
*/
```

### 异步任务封装方式
```js
var fetch = require('node-fetch');
function* gen(){
    var url = 'https://api.github.com/bblu/github';
    var result = yield oneop(url);
    console.log(result.bio);
}

//用法:首先执行 Generator 函数获取遍历器对象，
var g = gen();
//然后使用next方法执行异步任务的第一阶段。
var result = g.next();
//由于 Fetch 模块返回的是一个 Promise 对象因此要用 then 方法调用下一个 next 方法。
//注意value的来源看上文的next输出
result.value.then(function(data){
    return data.json();
}).then(function(data){
    g.next(data);
});