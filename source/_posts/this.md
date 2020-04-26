---
title: this指针
categories: JavaScript
date: 2017-7-27 18:31:03
tags: [this]
---

# 问题一：this指向谁？<br/><br/>

**javascript中的this对象是在运行时基于函数的执行环境绑定的：**

**(1)在非严格模式下，this指向全局对象。浏览器下，全局对象为window，且与全局变量绑定在一起；node下，全局对象为global，但不与全局变量绑定在一起。**
**(2)在严格模式下，this不指向全局对象。**

<font face="黑体">**刚接触JavaScript的时候，总会多多少少遇到this的指向问题，MDN给出了很多this的指向，包括全局上下文，函数上下文，箭头函数，对象，原型链等等的this指向，但我们更多的是无法弄清楚函数的指向。**</font>

知乎上有大神用一句话总结了this的指向：<font color=red>谁调用函数，this就指向谁。

<!--more-->

直接看代码：

例1：

```
var a = 'window';
function foo () {
    var a = 2;
    return this.a;
}
foo();	//window
```

注意，foo()其实就是window.foo(),window调用的foo,所以foo函数里面的this指向window。

上面代码的执行环境是浏览器环境，若在node执行环境，结果会是undefined。因为：

在浏览器环境下，全局对象是window，而且跟全局变量绑定在一起。

在node环境下，全局对象是global，但没有跟全局变量绑定在一起。

<font color=red>在本文章，执行环境默认为非严格模式，浏览器环境。</font>

接下来看另一种情况：

例2：

```
var a = 'window';
var O = {
    a: 5,
    foo() {
        return this.a;
    }
}

O.foo();	//5
var b = O.foo;
b();		//window
```

上面第一种写法，因为对象O对用了函数foo，所以this指向O。

第二种将O.foo赋给b，所以当foo函数被调用时实际是window.b(),所以仍然是window调用。

第三种，构造函数的this，构造函数的this默认绑定正在构造的对象

例3：

```
function C(){
  this.a = 37;
}
var o = new C();
o.a;	// 37
```

第四种，匿名函数的this，由于匿名函数的执行环境具有全局性，因此其this对象通常指向window。

例四：

```
var name = 'window';
var obj = {
    name: 'name',
    getName: function () {
        return function () {
          return this.name;  
        };
    }
}
obj.getName()();	// window
```

第五种，箭头函数的this，箭头函数是没有this的，因此箭头函数的this是词法作用域，会继承外层函数的this绑定。所谓的外层函数，无外乎<font colr=red>函数和全局对象</font>。

例5：

```
var a = 'window';
var obj = {
	a: 5,
	foo: () => this.a
}
obj.foo();	// window
```

```
var a = 'window';
var obj = {
	a: 5,
	foo: function(){
		return () => this.a;
	}
}
obj.foo()();	//5
```

上面两端代码的不同之处在于箭头函数外层有无函数。刚才说过箭头函数的this是继承外层函数的this绑定，所以遇到箭头函数的this，它会去寻找它的外层函数，一直到全局对象为止。

第一种情况，箭头函数外层依次是foo属性，obj对象，全局对象，foo，obj都不是函数，所以this继承全局对象的this。

第二种情况，箭头函数外层是foo，而foo的值是一个函数，所以this继承foo的函数，而obj.foo()()是obj对象调用的foo函数，<font color=red>谁调用函数，this就指向谁</font>，所以foo函数的this指向obj。

除上面四种情况，还有其他形式的this的调用，比如闭包的this，其结果类似例2的b().除此，还有原型链的this，事件处理对象的this等，有兴趣可以去了解一下。

最后，看一个复杂的例子：多个对象调用同一个函数

```
var a = 'window';
var O = {
  a: 5
  C: {
    a: 10,
    foo() {
      return this.a;
    }
  }
}
O.C.foo();	//10
```

<font color=red>多个对象调用调用同一个函数，this指向最近的引用，即最靠近的引用最重要。</font>

# 问题二：如何改变this的指向？<br/><br/>

有三种方法可以绑定this,call,apply和bind方法。

```
var O = {
	a: 5,
	foo(b) {
		return this.a + b;
	}
}
O.foo.call({a: 10}, 5);		//15
O.foo.apply({a: 20}, [6]);	//26
O.foo.bind({a: 10}, 5)();		//15
```

上面三种方法都可以改变this的指向，不同的call和bind的第二个参数传递的是函数参数，而apply必须是参数列表。另外call和apply返回的是函数执行结果，而bind返回的是函数，必须手动执行该函数。

另外箭头函数的this也是可以改变的，当然不是直接改变，而是改变它外层函数的this指向。

最后，有一道比较繁琐的this题型，这是腾讯Alloy Team上的一道题，挺有趣的，可以试试

```
var name = 'window'

var person1 = {
  name: 'person1',
  show1: function () {
    console.log(this.name)
  },
  show2: () => console.log(this.name),
  show3: function () {
    return function () {
      console.log(this.name)
    }
  },
  show4: function () {
    return () => console.log(this.name)
  }
}
var person2 = { name: 'person2' }

person1.show1()
person1.show1.call(person2)

person1.show2()
person1.show2.call(person2)

person1.show3()()
person1.show3().call(person2)
person1.show3.call(person2)()

person1.show4()()
person1.show4().call(person2)
person1.show4.call(person2)()
```



答案：

```
person1.show1() // person1
person1.show1.call(person2) // person2

person1.show2() // window
person1.show2.call(person2) // window

person1.show3()() // window
person1.show3().call(person2) // person2
person1.show3.call(person2)() // window

person1.show4()() // person1
person1.show4().call(person2) // person1
person1.show4.call(person2)() // person2
```