---
title: ES6常见特性
categories: JavaScript
date: 2017-9-12 21:07:06
tags: [es6]
---

## 一.const，let，import，class

**let**

ES5只有全局作用域，ES6新增了块级作用域，所谓块级作用域，即块内声明的变量只在块内有效。

```
{
    var a = 10;
    let b = 5;
}

console.log(a);		// 10
console.log(b);		// ReferenceError
```

<!--more-->

ES6新增的let声明的变量只在块级作用域有效，和var对比，还有另外几个特性：

**1.不存在变量提示**

**2.暂时性死区.**

**3.不允许重复声明.**

```
{
    console.log(b);		// ReferenceError
    let b = 5;
}
{
    let b = 5;
    let b = 5;			// ReferenceError
}
```

**const**

ES6新增const声明常量，const声明的常量必须初始化，初始化之后不能更改值，否则报错.

```
const b;		// ReferenceError

const b = 5;
b = 10;			// ReferenceError
```

**另外，const和let一样不存在变量提升，存在暂时性死区以及不允许重复声明.**

<font color=red>注意：</font>

```
const b = {};
b["name"] = "xiaoming";

const a = [];
a.push(5);
```

以上代码不会报错，因为const只保证声明的变量指向的地址不变，不保证变量地址的数据不变，但若：

```
const b = {};
b = {};			// ReferenceError

const a = [];
a = [5];		// ReferenceError
```

改变引用地址，照样报错.

**import**

ES6引入import用于加载js文件或者模块，与之相对的是export命令.

比如，B.js导入A.js:

```
//	A.js
export const firstName = "xiaoming";
export const lastName = "xiaogang";

//	B.js
import { firstName as first, lastName } from './A.js';
```

import接受一个{}加载模块。若要为引入的模块重命名，用as命令，如上面的firstName更名为first.

**class**

ES6新增class来声明对象原型.使用方法类似ES5的构造函数.

```
class Point {
    constructor (x, y) {
        this.x = x,
        this.y = y
    }

    toString () {
        return `${this.x}:${this.y}.`
    }
}

const point = new Point("年龄", 5);
console.log(`${point}`);	// 年龄:5.
```

**<font color=red>加上ES6定义的，JavaScript共有六种声明变量的方法：var, function, let, const, import, class.</font>**



## 二.解构赋值.

```
let [a, b] = [5, 6];					// 数组的解构赋值
let {foo, bar} = {foo: 'aaa', bar: 'bbb'};		// 对象的解构赋值
let [a, b, c, d] = 'name';				// 字符串的解构赋值
function add ([x, y]) { return x + y; }	add([5, 6]);	//函数的解构赋值
```



## 三.Set和Map. 

ES6新增----数据结构Set，类似于数组，区别是没有重复的元素.

​	     ----数据结构Map，类似于对象，但对象属性只接受字符串，Map可接受其它类型.

```
const s = new Set([5, 7, 10]);
s.add(12);		//	添加元素
s.has(5);		//	true
s.delete(5);		//	删除元素
s.has(5);		//	false
[...s];			//	[7, 10, 12]
s.size;			//	3
```

```
//	传统对象.
const data = {};
const element1 = document.getElementById("div");
const element2 = document.getElementById("p");
data[element1] = "elment1";
data[element2] = "element2";
data[element1];					//	element2
data[element2];					//	element2
data["Object HTMLDivElement"];			//	element2
```

```
// Map
const m = new Map();
const element1 = document.getElementById("div");
const element2 = document.getElementById("p");
m.set(element1, "element1");
m.set(element2, "element2");
m.get(element1);				//	element1
m.get(element2);				//	element2
m.get("Object HTMLDivElement");			//	undefined
```

## 四.数组的扩展和扩展运算符.

**Array.from可以将类数组转换为对象,比如Set,Map,arguments,NodeList等等.**

```
function add (x, y, z) {
    const t = Array.from(arguments);
}

const list = document.querySelectorAll('p');
const nodelist = Array.from(list);
```

**Array.of可以将一组值转换为数组.**

```
const s = new Array(8);             // []
const m = new Array(8, 10, 11);     // [8, 10, 11]
const a = Array.of(8);              // [8]
const b = Array.of(8, 10, 11);      // [8, 10, 11]
```

**fill用于填充数组.**

```
const b = new Array(3).fill(7);             // [7, 7, 7]
const a = ['a', 'b', 'c'].fill(7, 1, 2);    // ['a', 7, 'c']
```

**includes可判断NaN.**

```
[NaN].indexOf(NaN);         // -1
[NaN].includes(NaN);        // true
```

**除此之外，还有entries(), keys(), valuse()用于遍历数组.**

**扩展运算符类似于Array.form，将类数组转换为逗号隔开的参数序列.**

```
[1, 2, ...[5, 7], 8];       // [1, 2, 5, 7, 8]
[...new Set([8, 9, 10])];   // [8, 9, 10]
```



## 五.字符串模板.

ES6提供了字符串模板``.

```
const a = 'hello';
console.log(a + ' ' + 'world!');
console.log(`${a} world!`);
```



## 六. 箭头函数.

```
// 传统写法
[1, 2, 3].map(function (x) {
    return x * x;
});
// 箭头函数写法
[1, 2, 3].map( () => x * x );
```

注意:箭头函数没有this对象，也没有arguments对象，更不能当真构造函数使用,

也无法使用apply,call,bind方法改变其this指向.



## 七.Symbol.

ES5有undefined,null,object,number,string,boolean六种数据类型，ES6新增Symbol数据类型，

代表独一无二.

```
let s = Symbol();
typeof s;           // Symbol
```

Symbol可以接受字符串参数，作为对Symbol对象的描述，同样，Symbol可以显性转换为字符串，

和布尔值，但不能转换为数值，也不能进行运算.

```
let s = Symbol('foo');
let m = Symbol('foo');
s === m;                // false
String(s);              // 'Symblo(foo)'
Boolean(m);             // true
Number(s);              // ReferenceError
s + 2;                  // ReferenceError
```



## 八.Promise.

ES6新增Promise对象，用来传递异步操作的消息。Promise共有三种状态，Pending,Resolve,Reject,只要其中之一发生改变，状态就会凝固，不会再变。

基本用法：

```
function timeout (ms) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, ms, 'done');
    });
}

timeout(100)
    .then( v => {
        console.log(v);
    })
```

用Promise封装Ajax操作,

```
const getJson = function (url) {
    const promise = new Promise((resolve, reject) => {
        const client = new XMLHttpRequest();
        client.open('GET', url);
        client.onreadystatechange = function () {
            if (client.readyState === 4 && client.status === 200 ) {
                resolve(client.response);
            }   else    {
                reject(new Error(client.statusText));
            }
        }
        client.send();
    });

}

getJson("/post.json")
    .then(json => {
        console.log(json);
    })
    .catch(error => {
        console.log(error);
    })
```

## 九.for...of

ES6新增for...of循环.

for..in遍历键名，for...of遍历键值

```
const a = ['a', 'b', 'c'];
for( let i in a) {
    console.log(i);     // 0, 1, 2
}
for( let v of a) {
    console.log(v);     // 'a', 'b', 'c'
}
```

另外,for...in不仅遍历键名，还会遍历手动添加的其它键，而且，某些情况下，for...in还会以任意顺序遍历键名.

**以上是我学习ES6的一些总结，若想了解更多，可参考**[阮一峰ES6标准入门](http://es6.ruanyifeng.com/)

