---
title: Js 如何实现函数只执行一次
date: 2020-11-04 23:28:23
categories:
 - javascript
tags:
 - javascript
---

# Js 如何实现函数只执行一次



有时候我们会遇到某个函数在多次执行中只运行一次的情况，那么如何实现呢？ 下面直接上代码



```javascript
let once = function(fn) {
    let caller = true;
    return function() {
        if(caller) {
            caller = false
            fn.apply(this, arguments)
        }
    }
    
}
```



通过调用上面的函数



```javascript
let onlyOneFn = once(function() {
	console.log('once')
})

function runLoopFn() {
  let i = 100
  while(i--) {
    console.log('loop run')
    onlyOneFn()
  }
}

runLoopFn() // once * 1     loop run * 100
```





上面的执行结果可以看出， 循环执行100次， 打印了100次 loop run， 但是只执行了一次打印 once

通过封装的`once` 函数， 利用闭包我们可以实现函数只执行一次.