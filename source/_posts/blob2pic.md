---
title: 二进制流转图片
date: 2020-04-07 23:36:23
categories:
 - javascript
tags:
 - blob
---

**二进制流(ArrayBuffer)转为图片**

1. 首先通过ajax或者ws获取到传来的二进制流数据


2. responseType 需要设置为arraybuffer。

3. 然后我们拿到所需的二进制数据， 通过console打印发现是一个ArrayBuffer，接着我们对其进行如下处理，转为base64:

   <!-- more -->

```javascript
  function arrayBufferToBase64(buffer) {

      var binary = '';

      var bytes = new Uint8Array(buffer);

      var len = bytes.byteLength;

      for (var i = 0; i < len; i++) {

          binary += String.fromCharCode(bytes[i]);

      }

      return window.btoa(binary);

  }
```



4. 显示图片。   接着我们把收到的二进制数据通过上面的函数转为url:

``` javascript
let url= arrayBufferToBase64(response);
```

​			然后将生成的url放到img标签的src属性中：

```html
document.getElementById('img').src='data:image/jpeg;base64,'+url;
```

​			大功告成～