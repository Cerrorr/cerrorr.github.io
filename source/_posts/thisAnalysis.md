---
title: this绑定分析
date: 2023-03-08 22:16:01
cover: https://cdn.jsdelivr.net/gh/Cerrorr/cerrorr.github.io@images/202303082221876.png
categories: 重读你不知道的JavaScript
tags: JavaScript
---
## 默认绑定

```javaScript
function foo() {
    console.log(this.a);
}
var a = 2;
foo(); // 2
```

此时this指向全局对象，因为foo()是在全局作用域中调用的，所以this绑定到全局对象。

```javaScript
function foo() {
    "use strict";
    console.log(this.a);
}
var a = 2;
foo(); // TypeError: this is undefined
```

严格模式下，this绑定到undefined。

## 隐式绑定

```javaScript
function foo() {
    console.log(this.a);
}
var obj = {
    a: 2,
    foo: foo
}
obj.foo(); // 2
```

此时this指向obj，因为foo()是在obj对象中调用的，所以this绑定到obj。对象属性引用链中只有最顶层或者说最后一层会影响调用位置。

```javaScript
function foo() {
    console.log(this.a);
}
var obj = {
    a: 2,
    foo: foo
}
var bar = obj.foo;
var a = "oops, global";
bar(); // "oops, global"
```

此时this指向全局对象，一个常见的绑定问题，隐式绑定的函数会丢失绑定对象。因为bar()是在全局作用域中调用的，所以this绑定到全局对象。

## 显示绑定

```javaScript
function foo() {
    console.log(this.a);
}
var obj = {
    a: 2
}
foo.call(obj); // 2
```

此时this指向obj，因为foo()是在obj对象中调用的，所以this绑定到obj。call()、apply()、bind()方法可以用来指定函数的this绑定对象。

## new绑定

```javaScript
function foo(a) {
    this.a = a;
}
var bar = new foo(2);
console.log(bar.a); // 2
```

此时this指向bar，因为foo()是在bar对象中调用的，所以this绑定到bar。new绑定会创建一个全新的对象，它会被执行[[Prototype]]连接，最后会绑定到函数调用的this。new绑定优先级最高。

## 箭头函数

```javaScript
function foo() {
    return a => {
        console.log(this.a);
    }
}
var obj1 = {
    a: 2
}
var obj2 = {
    a: 3
}
var bar = foo.call(obj1);
bar.call(obj2); // 2
```

箭头函数没有this，它的this继承自外层函数调用的this。

## 被忽略的this

```javaScript
function foo() {
    console.log(this.a);
}
foo.call(null); // 2
```

null和undefined被忽略，因为它们在调用函数时没有指定this，所以会应用默认绑定规则。

## 间接引用

  ```javaScript
  function foo() {
      console.log(this.a);
  }
  var a = 2;
  var o = { a: 3, foo: foo };
  var p = { a: 4 };
  o.foo(); // 3
  (p.foo = o.foo)(); // 2
  ```

  间接引用会把引用环境进行一次解引用，因此p.foo()实际上是一个不带任何修饰的函数调用，因此应用默认绑定。

## 绑定优先级

new绑定 > 显示绑定 > 隐式绑定 > 默认绑定

## 判断this

1. 函数是否在new中调用（new绑定）？如果是的话this绑定的是新创建的对象。
2. 函数是否通过call、apply（显示绑定）或者硬绑定调用？如果是的话this绑定的是指定的对象。
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话this绑定的是那个上下文对象。
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到全局对象。
5. 如果以上都不是的话，就抛出一个错误。

