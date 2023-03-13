---
title: 原型&原型链
date: 2023-03-13 11:03:31
cover:
categories: 重读你不知道的JavaScript
tags: JavaScript
---

## 构造函数
```javascript
function Person(name, age) {
  //实例成员
  this.name = name;
  this.age = age;
}
//静态成员
Person.sex = '男';


let p1 = new Person('张三', 18);
console.log(p1.name); //张三
console.log(p1.sex); //undefined


console.log(Person.name); //undefined
console.log(Person.sex); //男
```
实例成员是通过构造函数添加的成员,静态成员是构造函数本上添加的成员.通过new关键字创建的实例对象可以访问到实例成员,但是不能访问到静态成员.构造函数本身可以访问到静态成员,但是不能访问到实例成员.

## new一个对象的过程
```javascript
 function Person(name) {
  this.name = name;
 }
  let p1 = new Person('张三');
  console.log(p1); //Person { name: '张三' }
```
1. 创建一个新对象 p1{}
2. 将新对象的原型指向构造函数的原型 p1.__proto__ = Person.prototype
3. 将构造函数的this指向新对象 Person.call(p1)
4. 为新对象添加属性和方法 p1.name
5. 如果构造函数返回的是一个对象,则返回该对象,否则返回空对象 Person { name: '张三' }

## 实例的方法是否共享?
```javascript
//构造函数上直接定义方法
function Person(name) {
  this.name = name;
  this.sayName = function() {
    console.log(this.name);
  }
}
let p1 = new Person('张三');
let p2 = new Person('李四');
console.log(p1.sayName === p2.sayName); //false
```
每个实例指向不同内存空间,所以实例方法不共享.

```javascript
//构造函数的原型上定义方法
function Person(name) {
  this.name = name;
}
Person.prototype.sayName = function() {
  console.log(this.name);
}
let p1 = new Person('张三');
let p2 = new Person('李四');
console.log(p1.sayName === p2.sayName); //true
```
实例方法共享,因为实例的原型指向构造函数的原型,所以实例的原型上的方法都是共享的.

## 原型链
```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.sayName = function() {
  console.log(this.name);
}
let p1 = new Person('张三');
console.log(p1.__proto__ === Person.prototype); //true
console.log(Person.prototype.__proto__ === Object.prototype); //true
console.log(Object.prototype.__proto__ === null); //true
```
原型链的终点是null,原型链的每一环都是一个对象,每个对象都有一个原型对象,原型对象也是一个对象,所以原型链是一个对象的链条.
## 原型的构造器
```javascript
function Person(name) {
  this.name = name;
}
console.log(Person.prototype.constructor === Person); //true
```
原型对象的constructor属性指向构造函数本身.
## instanceof
```javascript
function Person(name) {
  this.name = name;
}
let p1 = new Person('张三');
console.log(p1 instanceof Person); //true
console.log(p1 instanceof Object); //true
```
instanceof运算符用来判断一个对象是否是一个构造函数的实例,instanceof运算符的原理是判断构造函数的原型是否在对象的原型链上.

## isPrototypeOf
```javascript
function Person(name) {
  this.name = name;
}
let p1 = new Person('张三');
console.log(Person.prototype.isPrototypeOf(p1)); //true
console.log(Object.prototype.isPrototypeOf(p1)); //true
```
isPrototypeOf方法用来判断一个对象是否在另一个对象的原型链上.

## hasOwnProperty
```javascript
function Person(name) {
  this.name = name;
}
let p1 = new Person('张三');
console.log(p1.hasOwnProperty('name')); //true
console.log(p1.hasOwnProperty('sayName')); //false
```
hasOwnProperty方法用来判断一个对象是否有某个属性,不会去原型链上查找.
