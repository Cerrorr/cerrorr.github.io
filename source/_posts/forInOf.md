---
title: for...of和for...in的区别
date: 2023-03-09 23:02:01
cover: https://cdn.jsdelivr.net/gh/Cerrorr/cerrorr.github.io@images/202303092307629.png
categories: 重读你不知道的JavaScript
tags: JavaScript
---
## for...in

```javaScript
var obj = {
    a: 1,
    b: 2,
    c: 3
};
Object.defineProperty(obj, "d", {
    enumerable: false,
    value: 4
});
Object.defineProperty(obj, "e", {
    enumerable: true,
    value: 5
});
for (var k in obj) {
    console.log(k, obj[k]);
}
// a 1
// b 2
// c 3
// e 5
```

for...in循环会遍历对象自身的和继承的可枚举属性，但是不包括Symbol属性。使用for...in循环遍历对象时，会跳过不可枚举的属性，但是如果该属性的可枚举性是可以改变的，那么for...in循环会遍历到这个属性。

## for...of

```javaScript
var arr = [1, 2, 3, 4, 5];
for (var v of arr) {
    console.log(v);
}
// 1
// 2
// 3
// 4
// 5
```

for...of循环可以遍历数组、类数组对象、字符串、Set、Map、Generator对象，但是不能遍历对象。for...of循环内部调用的是数据结构的Symbol.iterator方法。

## 手写for...of

```javaScript
function forOf(obj, cb) {
    var iterator = obj[Symbol.iterator]();
    var result = iterator.next();
    while (!result.done) {
        cb(result.value);
        result = iterator.next();
    }
}
var arr = [1, 2, 3, 4, 5];
forOf(arr, function (v) {
    console.log(v);
});
// 1
// 2
// 3
// 4
// 5
```

## for...in和for...of的区别

- for...in循环遍历的是对象的属性名，for...of循环遍历的是对象的属性值。
- for...in循环遍历的是对象自身的和继承的可枚举属性，for...of循环遍历的是对象自身的可枚举属性。
