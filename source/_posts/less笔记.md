---
title: less 使用总结
date: 2020-11-04 22:59:29
tags: 
 - css
categories: 
 - less
---

# less  笔记📒





## 变量



```less
@color: red;
.a {
  color: @color;
}
```



<!-- more -->

## 变量选择器

```less
@my-selector: swipper;

.@{my-selector}-list {
  color: red;
}


// 转换成
.swipper-list {
  color: red;
}
```





## Properties

```less
@my-propertity: width;

.block {
  @{my-propertity}: 10px;
}

// 转换成
.block {
  width: 10px;
}
```

用变量代替或**拼接**样式属性名



## 用变量定义变量名



```less
@danger: red;

.font {
  @color: @danger;
  color: @@color;
}
```



## Properties as Variables

```less
@color: red;
.bg {
  color: @color;	// red
  background-color: $color;	// red
}
```

利用属性名做为变量将属性值传递过去



## 逻辑函数

```less
.main {
  color: if(boolean(true) , red, yellow)
}

// 转换成
.main {
  color: red;
}
```



## list functions 列表函数

```less
// length (返回列表长度)
@my-list: nav, bar;
@len: length(@my-list);
.w {
  width: e("@{len}px");	// e函数作用是去掉引号
}
// ==> 转换成
.w {
  width: 2px;
}


// extract (返回列表中指定位置元素)
@list-idx: 10px, 20px, 30px, 40px;
.l {
  width: extract(@list-idx, 2);
  
}
// ====> 转换成
.l {
  width: 20px;
}

// range(创建一个范围的列表)
@values: range(0, 100px, 20px);
.w {
  border: @values;
}
// =====> 转换成后
.w {
  border: 0px 20px 40px 60px 80px 100px;
}

// each(遍历列表，生成绑定的每个样式类)
@myList: left, top, right, bottom;
each(@myList, {
  .my-p {
   position: absolute;
   @{value}: 0;
  }
})
// =====> 转换成
.my-p {
  position: absolute;
  left: 0;
}
.my-p {
  position: absolute;
  top: 0;
}
.my-p {
  position: absolute;
  right: 0;
}
.my-p {
  position: absolute;
  bottom: 0;
}

```



## 循环输出



````less
// eg: 想输出如下的样式
.block-a {
  color: a;
}
.block-b {
  color: b;
}

// 实现
@colors: a,b,c,d;
// 定义函数
.blockCreater(@name, @color) {
  .block-@{name} {
    color:  @color;
  }
}
// 循环生成
.loop(@i) when (@i < length(@colors)+1){
    .blockCreater(extract(@colors, @i),extract(@colors, @i));
    .loop(@i+1);
}
.loop(1);
// ====> 转换成
.block-a {
  color: a;
}
.block-b {
  color: b;
}
.block-c {
  color: c;
}
.block-d {
  color: d;
}

````

