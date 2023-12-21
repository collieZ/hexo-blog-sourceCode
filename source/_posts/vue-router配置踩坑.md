---
title: vue-router配置踩坑
date: 2021-03-30 17:04:08
tags:
 - Vue
 - Vue-Router
categories:
 - javascript

---



# Vue-Router 配置踩坑、探究



先来看一段 Vue-Router 官方的介绍

![image-20210430153555730](https://tva1.sinaimg.cn/large/008i3skNly1gq1v8sdpisj30m70b9dgt.jpg)

<!-- more -->

![image-20210430153605660](https://tva1.sinaimg.cn/large/008i3skNly1gq1t65okoqj30lb04674g.jpg)



可以得出以下几个结论：

1. 路由是按照从上到下定义的顺序来进行匹配的，也就是说，当前路由当匹配到路由配置中靠前的路由后就会停止后续路由的匹配，即使后面有相同的路由。
2.  `path: *`  默认会匹配所有路径，那按照第一点来说，该路由配置只能放在最后，否则会影响路由正常跳转。



但是！！

在一个项目中，我看到了如下配置

![image-20210430154731056](https://tva1.sinaimg.cn/large/008i3skNly1gq1ti14xcsj31120ootaj.jpg)

？？？ 按照上面分析这样不是会导致所有路由都定位到 404 吗

但是实际我们启动了项目后，路由却能正常跳转，这不是跟上述的规则对不上吗？



本着探究到底的精神，经过多方询问和查阅，我们在源码中发现了这段代码

![image-20210430164730265](https://tva1.sinaimg.cn/large/008i3skNly1gq1v8odst6j31j60q4gqu.jpg)



原来源码里面已经帮我们进行了处理，不管如何配置 `path: *` 都会移到最后。

