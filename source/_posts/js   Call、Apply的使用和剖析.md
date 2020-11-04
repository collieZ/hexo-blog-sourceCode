---
title: JS   call、apply的使用和剖析
date: 2020-11-04 23:35:23
categories:
 - javascript
tags:
 - javascript
---

# JS   call、apply的使用和剖析





## call



### 用法

```javascript
let obj = {name: 'chou'}

let fn = function(first, last) {
  console.log(this.name + first + last)
}

fn.call(obj, 'first', 'last')		// choufirstlast

```



通过调用call方法，将函数`fn`的this指向传给call的第一个参数`obj`，同时将`call`方法的第二、第三……个参数作为`fn`函数的参数进行传递。 `fn`函数的this会指向obj，这样`this.name`就是obj中的name属性，`fn`的参数也就是call传入的参数。

> 需要注意⚠️：1. 调用call方法后，fn函数会立即执行。
>
> ​						 2. call方法传入函数参数的方式是通过参数列表传入，与后面apply通过数组方式这点不同。



### 用途

1. 最常见的用途就是改变调用函数`this`的指向
2. 立即调用函数 (什么参数都不传)
3. 借用别的对象的属性或方法



### 实现



思路： 

call的原理是改变函数`this`指向，并传入参数，然后立即执行这个函数，我们可以顺着这个思路将要改变`this`指向的函数挂载到要指向的对象上，然后传入参数并执行(因为调用call后会立即执行)。



```javascript
Function.prototype.mycall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  context = context || window
  context.fn = this // 要执行的函数 eg: fn.call(this)
  let arg = [...arguments].slice(1) // call传入的参数 [this,1,2,3,4] ===> [1,2,3,4]
  let result = context.fn(...arg) // 传入参数给fn，执行函数
  delete context.fn
  return result
}

let obj = {name: 'chou'}

let fn = function(first, last) {
  console.log(this.name + first + last)
}

fn.mycall(obj, 'first', 'last')		// choufirstlast
```





## apply



### 用法

*apply的用法和call几乎一摸一样，只是传入参数的方式不一样*



```javascript
let obj = {name: 'chou'}

let fn = function(first, last) {
  console.log(this.name + first + last)
}

fn.apply(obj, ['first', 'last'])		// choufirstlast
```



> apply传给函数`fn`参数是通过传入一个数组的方式，其他方面和call一摸一样。  具体解释参考call



### 用途

1. 最常见的用途就是改变调用函数`this`的指向
2. 立即调用函数 (什么参数都不传)
3. 借用别的对象的属性或方法



### 实现

思路： 

apply的实现和call处理几乎一样，唯一需要注意的就是参数的处理方式不同，我们只需要针对apply(this, params) 着重`params`的处理就行了。



```javascript
Function.prototype.myapply = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('not funciton')
  }
  context = context || window
  context.fn = this // 要执行的函数 eg: fn.call(this)
  let arg = [...arguments][1] // call传入的参数 [this, [1,2,3,4]] =====> [1,2,3,4]
  let result = context.fn(...arg) // 传入参数给fn，执行函数
  delete context.fn
  return result
}

let obj = {name: 'chou'}

let fn = function(first, last) {
  console.log(this.name + first + last)
}

fn.myapply(obj, ['first', 'last'])		// choufirstlast
```





## 总结



相同点

* 都是改变函数`this`指向
* 都是立即执行函数



不同点

* call 传入参数是通过参数列表方式传入 (`fn.call(this,a1,a2,a3)`) ， apply传入参数是通过数组方式传入 (`fn.apply(this,[a1,a2,a3])`)