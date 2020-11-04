---
title: JS new 运算符的使用和原理剖析
date: 2020-11-04 23:48:23
categories:
 - javascript
tags:
 - javascript
---

# JS new 运算符的使用和原理剖析



> new 运算符创建一个用户定义的对象类型的实例或具有构造函数的内置对象类型之一





### new的基本使用



```javascript
let fn = function(hobby) {
	 this.name = 'lili'
   this.age = 22
   console.log('hobby is:', hobby)
}

fn.prototype.say = function() {
  console.log('hello')
}

let person = new fn('guitar')	// hobby is: guitar

console.log(person.name);	// lili
console.log(person.age);	// 22

person.say()	// hello
```





#### 当构造函数 `fn` 有返回值时



```javascript
let fn = function() {
	 this.name = 'lili'
   this.age = 22
   return {
     sex: 'man',
     city: 'cq'
   }
}

let person = new fn()	

console.log(person.name);	// undefined
console.log(person.age);	// undefined
console.log(person.sex);	// man
console.log(person.city);	// cq
```





#### 当构造函数 `fn` 返回值**不是对象**或是null时



```javascript
let fn = function() {
	 this.name = 'lili'
   this.age = 22
   return 'test'
}

let person = new fn()

console.log(person.name);	// lili
console.log(person.age);	// 22

// 返回null
let fn = function() {
	 this.name = 'lili'
   this.age = 22
   return null
}

let person = new fn()

console.log(person.name);	// lili
console.log(person.age);	// 22
```



> 从以上 new 的使用中我们可以总结如下几点：
>
> 1. new出的实例将会继承构造函数的属性和构造函数原型上的属性
> 2. 将会返回一个新对象
> 3. 如果构造函数返回新的对象，那么在实例中只能访问返回的新对象
> 4. 如果构造函数返回基本类型或者null，那么实例可以访问构造函数中的属性



针对如上几点，我们开始实现 new



### new 的实现



```javascript
let fn = function(hobby) {
	 this.name = 'lili'
   this.age = 22
   console.log('hobby is:', hobby)
}

fn.prototype.say = function() {
  console.log('hello')
}

let objFactory = function() {
  let obj = new Object()
  Constructor = [].shift.call(arguments);	// 取出第一个参数(构造函数)
  obj.__proto__ = Constructor.prototype;	// 将构造函数原型指向实例对象原型
  let res = Constructor.apply(obj, arguments)	// 将构造函数 this 指向实例对象，以便实例对象可以访问构造函数自身的属性
  return typeof res === 'object' ? res || obj : obj // 如果构造函数返回的是对象(非null), 则返回该对象，否则返回obj
}


let newFn = objFactory(fn, 'guitar') // hobby is: guitar
console.log(newFn.name); // lili
console.log(newFn.age); // 22
newFn.say(); // hello

```



