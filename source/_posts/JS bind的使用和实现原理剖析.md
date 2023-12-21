---
title: JS bind的使用和实现原理剖析
date: 2020-11-04 23:37:23
categories:
 - javascript
tags:
 - javascript
---

# JS bind的使用和实现原理剖析



```javascript
var obj = {name: 'jeff'}

var fn = function(arg1, arg2) {
  console.log('args==>', [arg1, arg2], 'name==>', this.name)
}

var bindFn = fn.bind(obj, 'arg1')	

bindFn('arg2') // args==> (2) ["arg1", "arg2"] name==> jeff
```



以上是bind的一个使用场景, 从上面代码中可以发现bind主要有以下几个作用:

1. 改变了函数`fn`的 `this` 指向；
2. 通过bind将参数传递给函数`fn`
3. 返回一个新函数

<!-- more -->

*通过 `fn.bind(obj, 'arg1')	` 将函数`fn`的this指向obj， 同时将 ‘arg1’ 作为 bind 的第二个参数传递给函数 `fn`， 并返回新函数 `bindFn`   。 此时调用 `bindFn`  并传入参数作为 fn 的第二个参数，执行结果依次是打印 arg1 、 arg2、jeff*



### 实现一个bind



如果要实现bind， 我们根据其使用方式总结出如下几点：

1. 改变函数 this 的指向
2. 返回一个新的函数
3. 将传给 bind 的参传递给函数



#### 改变this指向并返回新函数

```javascript
Function.prototype.mybind = function (context) {

    var self = this;
    // 获取函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);

    return function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(context, args.concat(bindArgs));
    }
}

var obj = {name: 'zzw'}

var fn = function(arg1, arg2) {
  console.log(this.name, 'args==>', arg1, arg2)
}

// 使用 mybind绑定函数 fn 到 obj对象 并传入第一个参数 'firstarg' 给函数 
var bindFn = fn.mybind(obj, 'firstarg')

// 调用返回的函数 bindFn， 并传入参数 'secondarg'
bindFn();	// zzwargs==>firstargsecondarg
```





> ! -->  bind 返回的函数不仅可以直接执行， 还可以用作构造函数，同时还能传递参数，例如



```javascript
let obj = {name: 'zzz'}

var fn = function(arg1, arg2) {
  console.log(this.name)
  console.log(arg1)
  console.log(arg2)
} 

fn.prototype.age = 20

var bindFn = fn.bind(obj)

// 调用 new时, bind 的 this会失效. 传入的参数
var newFn = new bindFn('argnew')	// undefined argnew undefined

console.log(newFn.age) // 20


```





我们针对这个问题对之前的 `mybind` 函数进行一下优化

如果调用构造函数的话， this会指向构造出来的对象， 对应上面的就是 `newFn`



```javascript
Function.prototype.mybind = function (context) {

    var self = this;
    // 获取函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);
		
  	var fBound = function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
      	// 如果this指向fBound，则作为构造函数指向本身， 如果不是则作为普通函数指向要绑定的对象
        return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 继承 绑定函数 的原型
    fBound.prototype = this.prototype
    return fBound
}

// !优化一下  ⬇️⬇️ 防止修改原型时将原函数原型影响了
Function.prototype.mybind = function (context) {

    var self = this;
    // 获取函数从第二个参数到最后一个参数
    var args = Array.prototype.slice.call(arguments, 1);
		var fNOP = function () {};
  	var fBound = function () {
        // 这个时候的arguments是指bind返回的函数传入的参数
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fBound ? this : context, args.concat(bindArgs));
    }
    // 继承绑定函数的原型
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound
}

```





