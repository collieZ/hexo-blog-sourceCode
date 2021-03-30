---
layout: threejs
title: ThreeJs基础概念
date: 2021-03-30 11:53:10
tags:
 - ThreeJs
 - JavaScript
categories:
 - ThreeJs
---



# three.js  基础概念



> Three.js 是基于webgl的二次封装。将webgl繁杂的api封装成较为简洁、面向对象的方式。threejs提供了几个基本对象让我们很方便的构建一个3D应用，分别是 **场景** 、 **相机**、 **渲染器** 。
>
> 场景就是呈现图形画面的空间、相机可以理解为观察拍摄图形画面的方式、渲染器则是将画面渲染到屏幕上。
>
> 我们将从以上三个对象出发，了解threejs如何绘制一个基础的3D图形



<!-- more -->

### 场景、相机



```javascript
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );
```



以上代码新建了一个场景和透视相机，主要看下相机这一部分。



#### 相机详解

​	three.js 内置了多种相机，使用最普遍的是透视相机（PerspectiveCamera）和正交相机 （OrthographicCamera）

​	相机都是使用右手坐标系：

​	![](https://bkimg.cdn.bcebos.com/pic/08f790529822720e84007b1870cb0a46f31fab02?x-bce-process=image/resize,m_lfit,w_640,limit_1)



##### 透视相机

透视相机观察的效果更人眼一样，观察物体时是 **近大远小** 的效果， 如下图所示：

![](https://user-gold-cdn.xitu.io/2018/12/25/167e5f89cca8dd1f?imageView2/0/w/1280/h/960/format/)



`new THREE.PerspectiveCamera(fov, aspect, near, far)` 该api为新建一个透视相机，相应的参数分别代表

- `fov`表示视角；
- `aspect`表示裁切面宽高比；
- `near`表示近平面距离；
- `far`表示远平面距离；



> 只有离相机的距离大于`near`且小于`far`，另外还得在相机视角(fov)内的物体，才能被相机观察到从而被渲染器渲染出来；



##### 正交相机

正交相机观测的物体不管离物体距离远近，渲染出来都是一个大小



![](https://www.ituring.com.cn/download/01YiZI4zr57Q.small)



`THREE.OrthographicCamera(left, right, top, bottom, near, far)`

这六个参数分别代表正交投影照相机拍摄到的空间的六个面的位置，这六个面围成一个长方体，我们称其为**视景体**（Frustum）。只有在视景体内部（下图中的灰色部分）的物体才可能显示在屏幕上，而视景体外的物体会在显示之前被裁减掉。

为了保持照相机的横竖比例，需要保证`(right - left)`与`(top - bottom)`的比例与Canvas宽度与高度的比例一致。

同时`near`与`far`都是指到照相机位置在深度平面的位置。



##### 相机位置

相机的`position`属性决定其位置, 参数分别为 x、y、z。   `camera.position.set(x, y, z)`



相机的up属性决定相机的正视方向，三个参数分别为x、y、z三个维度的向量。   `camera.up.set(0, 1, 0)`  // y轴正向，人正立观察对象



`Vector3`对象。   `Vector3( x : Float, y : Float, z : Float )`  这个属性既可以表示三维空间中的`点`也可以表示`向量`



相机还有很重要的一个属性`视点`需要设置，也就是人眼的`焦点`，调用`lookAt`方法即可，参数为`Vector3`对象，`点(0, 0, 0)`表示`坐标系原点`。





### 渲染器

`视网膜`的作用是`成像`，在 ThreeJS 框架中的`渲染器`起到相同的作用

Threejs提供了几种渲染器，最常用的是 `WebGLRenderer` , 其他的视情况看兼容性而定。

```javascript
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight); //设置渲染器宽度和高度
document.body.appendChild(renderer.domElement); // 将渲染器防止到body容器下
```





### 物体

Threejs封装了多种几何体，以 `BoxGeometry` 举例，可以创建正方体或者长方体

```javascript
var geometry = new THREE.BoxGeometry( 100, 100, 100);
var material = new THREE.MeshLambertMaterial( {color: 0xff0000} );
cube = new THREE.Mesh( geometry, material );
cube.position.set(0,0,0);
```



`BoxGeometry`对象时只用了前三个参数，分别表示`长`、`宽`、`高`；但其实该对象还有三个参数

```javascript
BoxGeometry(width, height, depth, widthSegments, heightSegments, depthSegments)
```

分别表示每个面由多少三角形组成。在webGL中，最基础的就是`点`、`线`、`三角形` ，threejs中负责的图形都是由这些组成的。上述生成的`三角形网格` 组成整个正方体轮廓，此时我们还需要给正方体表面添加`材质` 。

上面例子中使用的是 `MeshLambertMaterial`  兰伯特材质, 然后设置颜色为 `0xff0000`



接着将`材质`和`轮廓` 结合在一起就生成了正方体，并设置其中心点在坐标系原点。

然后将物体添加到场景中 `scene.add(cube)`



### 总结



threejs 主要由渲染器、场景、相机组成。通过配置好场景、相机，然后将生成的物体放置在场景中，最后通过渲染器生成到屏幕上，就完成一个3D图形了。

下面是threejs完整代码

```javascript
const scene = new THREE.Scene(); // 创建场景
const camera = new THREE.PerspectiveCamera(75,  window.innerWidth / window.innerHeight,  0.1,  1000); // 创建相机

// 创建渲染器
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight); //设置渲染器宽度和高度
document.body.appendChild(renderer.domElement);

// 设置相机
var origPoint = new THREE.Vector3(0, 0, 0); //原点
camera.position.set(20, 40, 60); //设置相机位置
camera.up.set(0, 1, 0); //设置相机正方向 y正方向向量
camera.lookAt(origPoint);//设置相机视点

// 设置物体
const geometry = new THREE.BoxGeometry(); // 生成几何体轮廓
const material = new THREE.MeshBasicMaterial({ color: 0x66ccff }); // 生成几何体材质
const cube = new THREE.Mesh(geometry, material); // 轮廓、材质生成立方体
scene.add(cube); // 添加到场景中

// 渲染
renderer.render(scene, camera);
```

