---
title: react hooks知识点
date: 2020-07-01 23:48:23
categories:
 - javascript
 - react
tags:
 - react
---

## react  知识点(hooks, class)



### 1. usecallback



> 缓存函数，依赖项变化后再重新生成.
>
> [`useCallback`](https://react.docschina.org/docs/hooks-reference.html#usecallback) Hook 允许你在重新渲染之间保持对相同的回调引用以使得 `shouldComponentUpdate` 继续工作
>
> 当缓存的函数依赖项频繁变动的话，可以采用useRef的形式，在 `xxxRef.current` 上把所依赖的变量写入xxxref， usecallback依赖 xxxRef， 因为xxxref只会创建一次，且xxxref.current始终是最新的，所以可以解决依赖项频繁变更的问题并且可以完成函数的缓存。



<!-- more -->

### 2.memo



> 子组件 props不变的情况下，父组件重新渲染子组件并**不会**重新渲染





### 3. useMemo



> 缓存值，当依赖项变化时，才会重新计算生成最新值





### 4.PureComponent



> 同memo， 通过class继承的方式实现子组件**不因为**父组件更新而进行不必要的渲染。，只依赖props



#### 为什么不每个组件都用memo或者pureComponent包装呢？

​	react决定组件是否更新是根据 **前后props** 来比较的，但比较也仅仅只是**潜比较** ， 只能比较值的不同，如果是引用，比如对象、数组等就无能为力老。 所以，如果props是引用类型，父组件每次更新，传递的引用对象都是新生成的，地址空间已经变了，所以子组件经过潜比较判断前后的prop是不相等的，就会造成子组件重新渲染。 同样， 传递的箭头函数(匿名函数)也会造成相同的后果。 那我们如何规避这种情况呢？

* 拆分props传入的应用对象，拆分为单个的值
* 函数传入唯一声明的函数引用。 比如在class中传入 <code> xxxxfn.bind(this)</code>的函数
* 控制更新。 使用class的 `shouldComponentUpdate(prevProps, prevState)`  或者memo的 第二个参数来对前后props进行更深层次的比较，从而控制更新。



### 5. useReduce



> [`useReducer`](https://react.docschina.org/docs/hooks-reference.html#usereducer) Hook 减少了对深层传递回调的依赖，









### 6. useContext



> 用于解决组件深层次嵌套的通信问题



