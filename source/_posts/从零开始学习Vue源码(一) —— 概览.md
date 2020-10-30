---
title: 从零开始学习Vue源码 —— 概览
date: 2020-07-01 23:50:23
categories:
 - javascript
 - vue
tags:
 - vue
---

# 从零开始学习Vue源码 —— 概览





先上一张图



![](https://user-gold-cdn.xitu.io/2017/12/19/1606e7eaa2a664e8?imageslim)



以上流程包括了从：

> 初始化 —— 挂载 —— 解析模版 —— 生成渲染函数 ——   render function转换为VNode节点最终形成Virtual Dom Tree ——  virtual dom tree转化为真实Dom节点从而渲染视图



响应式更新包括了:

> 在init时进行了`Object.defineProperty` 的绑定 —— render function渲染读取值，触发getter函数 —— 进行依赖收集 —— 修改对象值触发setter函数 —— 通知**[依赖收集]**中Dep的每个 watcher 值变化了，需要更新视图 —— watcher调用 `update`来更新视图 ——  将新、旧VNode传入patch进行diff比较，只修改比较得出的差异的部分Dom



<!-- more -->

## init

在 `new Vue()`  之后， 会进行一系列的初始化操作，包括初始化 **生命周期** 、 **事件**、**props** 、 **methods** 、 **data** 、 **computed** 、 **watch** 等。 最重要的是进行 `Object.defineProperty` 的绑定，用于实现 **[响应式]** 和 **[依赖收集]** 



## $mount



在初始化之后就会进行组件的挂载



## compile

如果是运行时会进行`template`模版的编译， 包括 ：

* **parse** ——  使用正则解析模版中的指令、class、style等 ， 形成AST (抽象语法树)
* **optimize** —— 标记 static 静态节点，用于后面`update` 时 `patch` 执行diff算法对静态节点跳过，从而优化性能
* **generate** —— 将AST转换为 render function， 包括 render字符串 和 staticRenderFns字符串



经过以上步骤之后，就会生产用于渲染VNode的 render function





## Virtual DOM

Render function 转换为Vnode节点，最终形成 virtual Dom Tree ，最后虚拟Dom渲染得到真实Dom





## 响应式



render function被渲染时会读取 **Init**时绑定的值，就会触发 getter函数进行 **[依赖收集]** 

![](https://user-gold-cdn.xitu.io/2017/12/21/160770b2a77e084e?imageView2)

 **[依赖收集]** 是为了将观察者watcher对象存放到订阅者Dep的subs中



在修改对象值时会触发setter函数，通知之前依赖收集dep中的每个watcher值发生改变了，紧接着，watcher就会调用 `update`来更新视图



*关于setter为什么要通知dep中的每个watch，这里引用一段网友的解释:*

> 通知所有的watcher是指对应的dep保存的所有watcher。进行数据劫持的时候，循环遍历data中所有的属性，一个属性就会创建一个唯一的dep,当初始化解析编译界面的时候，一个指令/表达式就是一个watcher,那么解析指令/表达式的值的时候就去读data中的属性，触发getter，再触发添加dep添加订阅（将watcher添加到dep.subs中），当修改对应的属性的时候，触发setter，那么就再去触发这个dep保存的所有watcher





## 总结



以上大概梳理呢Vue整个运行机制，后续会针对每个大板块，小功能进行更细致化的分析。

