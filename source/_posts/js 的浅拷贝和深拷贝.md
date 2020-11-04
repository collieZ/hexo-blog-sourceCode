---
title: js 的浅拷贝和深拷贝
date: 2020-11-04 23:32:23
categories:
 - javascript
tags:
 - javascript
---

# js 的浅拷贝和深拷贝



### 深、浅拷贝的区别

> 浅拷贝只能拷贝对象第一层，如果对象之中还嵌套有对象则不能实现深层对象的拷贝
>
> 深拷贝可以实现对象深层次的拷贝，怎样修改拷贝后的对象，原对象都不会改变






#### 浅拷贝





```javascript
// 实现
function clone(obj) {
  let obj2 = Array.isArray(obj) ? [] : {}
  if(obj && typeof obj === 'object') {
  	for(let item in obj) {
    	obj2[item] = obj[item]
  	}  
  } else {
    obj2 = obj
  }
  return obj2
}

// 使用
let obj1 = {a: 1, b: {c: 123}}
let obj2 = clone(obj1)
obj2.b.c = 6666
obj1.b.c // 6666	修改拷贝对象后，原对象深层的对象还是改变了
```







### 深拷贝





```javascript
// 实现
function deepClone(obj) {
  let obj2 = Array.isArray(obj) ? [] : {}
  if(obj && typeof obj === 'object') {
  	for(let item in obj) {
    	obj2[item] = deepClone(obj[item])	// 递归遍历属性是否为对象
  	}  
  } else {
    obj2 = obj
  }
  return obj2
}

// 使用
let obj1 = {a: 1, b: {c: 123}}
let obj2 = clone(obj1)
obj2.b.c = 6666
obj1.b.c // 123	修改拷贝对象后，原对象深层的对象没有改变，说明对深层的对象也进行了拷贝
```



