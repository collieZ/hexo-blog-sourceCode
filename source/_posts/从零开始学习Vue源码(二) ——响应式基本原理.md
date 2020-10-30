---
title: 从零开始学习Vue源码 ——响应式基本原理
date: 2020-07-01 23:55:23
categories:
 - javascript
 - vue
tags:
 - vue
---

# 从零开始学习Vue源码 ——响应式基本原理





#### 这里实现一个简易版本的响应式系统来形象的理解数据是如何在初始化的时候被劫持，以及如何在修改时更新视图的。



### 首先看下Vue2.x实现响应式系统最重要的方法



``Object.defineProperty``, Vue2.x就是基于它实现的响应式

```javascript
/*
    obj: 目标对象
    prop: 需要操作的目标对象的属性名
    descriptor: 描述符
    
    return value 传入对象
*/
Object.defineProperty(obj, prop, descriptor)
```



具体的属性以及使用方法可以参考[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)



<!-- more -->

#### 下面贴一个简易版响应式的代码实现



```javascript
// 更新视图时调用
function cb (val) {
    /* 渲染视图 */
    console.log("视图更新啦～");
}

// 对对象中某个属性进行数据劫持
function defineReactive (obj, key, val) {
    Object.defineProperty(obj, key, {
        enumerable: true,       /* 属性可枚举 */
        configurable: true,     /* 属性可被修改或删除 */
        get: function reactiveGetter () {
            return val;         /* 实际上会依赖收集 */
        },
        set: function reactiveSetter (newVal) {
            if (newVal === val) return;
            cb(newVal);
        }
    });
}

// 遍历对象所有属性，进行数据劫持
function observer (value) {
    if (!value || (typeof value !== 'object')) {
        return;
    }
    
    Object.keys(value).forEach((key) => {
        if(typeof value[key] === 'object') {
            observer(value[key]) // 递归遍历深层次属性
        }
        defineReactive(value, key, value[key]);
    });
}

class Vue {
  constructor(options) {
    this._data = option.data
    observer(this._data);
  }
}

const vm = new Vue({
  data: {
    name: {firstName: 'z', lastName: 'zz'}
  }
})

vm._data.name.firstName = 'cc' // 此时会打印 视图更新啦～


```



##### 下面我们一步步解析以上简易版响应式系统原理



1. 首先我们要实现一个对对象属性进行数据劫持，跟踪属性更新的函数 `defineReactive`  ， 该函数使用了 `Object.defineProperty` 进行数据劫持, 被设置的属性会在读取时触发 `getter`,  会在设置时触发 `setter`    执行里面更新视图的函数 `cb` 打印出  *视图更新啦～*



2. 只有一个`defineReactive`还不够， 我们还需要实现一个 `observer`  来对对象所有属性遍历、递归进行绑定, 这样对象上所有属性修改时都会触发getter 、 setter。
3. 最后我们构建一个Vue 的class类， 在构造函数中初始化data，并进行**[响应式]** 化
4. 当实例化Vue后，我们修改_data上的值就会触发setter 更新视图



## 总结

响应式系统最重要的就是数据劫持部分，当然这里只是一个简单的实现，像数组对象等都没处理，不过能通过这个demo了解相应式的基本原理。

