---
title: 前端知识点梳理 --part one
date: 2020-06-10 23:36:23
categories:
 - javascript
tags:
 - summary
---

# 前端知识点梳理   --part one



## 基础部分

### 1.  Html 语义化的好处 ?

* 使html的结构更清晰，更直观，更易于seo





###  2. 清除浮动的方法有哪些 ？

* 定义一个空标签再浮动元素之后。 然后给其 <code>clear: both</code> 
* 定义伪元素来清除浮动

> 使用overflow:auto（空标签元素清除浮动而不得不增加无意代码的弊端,,使用zoom:1用于兼容IE）。



<!-- more -->

### 3. img标签 alt 和 title的异同

*  alt 是当图片显示不出来时显示的文字。
* title 是hover时显示的提示文字。 (描述性文字， 一直都有)

* 总结： alt是当正常图片显示不出来时的替代内容。title是一直存在的描述性内容。



### 4. 行内元素有哪些？块级元素有哪些？ 空(void)元素有那些?

*  行内元素: i、strong、b、bm、a、span、input、select、img
* 块级元素: div、h(x)、p、ul、ol、li
* 空元素 br



### 5. html5有哪些新特性、移除了那些元素？如何处理HTML5新标签的浏览器兼容问题？如何区分 HTML 和 HTML5？

1. 新增的元素

* 语义化元素: article、section、header、nav、footer
* 音视频元素：video、audio
* 画布元素： canvas
* 表单控件： time、email、date、calendar……
* 缓存 local storage、session storage
* 新技术： webworker(为 JavaScript 创造多线程环境)、geolacation、websocket

2. 移除的元素

* frame、frameset，noframes
* 纯表现的元素： basefont，**big**，center，font, s，strike，tt，u

3. 如何支持新的标签

* 直接使用成熟的框架、使用最多的是html5shim框架

4. 如何区分是html5

* docType/新增的元素



### 6. 介绍一下CSS的盒子模型？

* IE盒模型 ｜ 标准盒模型
* IE盒模型的计算方式是 border + padding + content  = 宽 ｜ 高
* 标准盒模型计算方式是 content = 宽 ｜ 高



### 7. css3新增属性?

* Border- 边框(shadow、img、radius)
* Background-背景 (image、size、origin(相对不同盒模型)、clip(相对不同盒模型裁剪)) --- (border-box、content-box、padding-box)
* gradients 渐变。   线性渐变 -- **Linear Gradients**  。  径向渐变 -- **Radial Gradients**
* 文字效果  (text-shadow: 文字阴影、box-shadow: 盒子阴影 、text-overflow:  文字溢出处理、 word-wrap:  文字换行、 word-break： 拆分换行,非中日韩文本)  字体: @font-face
* 2D、3D转换  .   2D : translate(移动)、 rotate(旋转)、 scale(缩放)、 matrix(包含旋转，缩放，移动（平移）和倾斜功能。 3D: 2D属性的3D体现
* 过渡: transition
* 动画  @keyframes 规则 。 animation ： 使创建的规则生效
* 多列布局  column-xxx
* 弹性布局 flex
* 媒体查询 @media xxx and (max-with: xxxpx) {}

