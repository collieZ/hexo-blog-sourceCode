---
title: 使用rollup打包并发布一个js库
date: 2021-04-30 15:27:41
tags:
 - javascript
 - rollup
 - npm
categories:
 - javascript
---



# rollup 打包js库



> 发布一个js库包含一下几个大致的流程
>
> 1. 编写库代码
> 2. 配置打包
> 3. 配置 npm 账户和发布配置
> 4. 配置 travis ci 持续集成环境，相关配置
> 5. 更新 version，提交tag 出发构建发布流程



<!-- more -->

## 1. 编写代码库

```javascript
// src/index.js

class Dog {
  constructor(name = 'huahua') {
    this.name = name
  }
  eat() {
    console.log(this.name + 'eat food!')
  }
}

export default Dog
```

## 2. 配置打包

打包有几种方式，目前主流是 webpack 和 rollup。 其中webpack适合开发项目打包（代码拆分、静态资源处理），如果是发布类库的话 rollup 更合适。 相关对比可以参考 https://www.jianshu.com/p/19a3d8ef5e6c

### rollup

​	这里我们开发类库使用的是 rollup 进行打包，详细介绍和使用可以参考： https://www.rollupjs.com/guide/tutorial

默认已经对 rollup 有所了解了，接着我们开始进行打包的配置：

1. 首先我们先在根目录新建 rollup.config.js 文件，并配置如下：

```javascript
import pkjson from './package.json';
import filesize from 'rollup-plugin-filesize';
import { babel } from '@rollup/plugin-babel';
import { nodeResolve } from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import { terser } from "rollup-plugin-terser";

const isDev = process.argv.splice(2).indexOf('--pub') < 0;
const prjName = pkjson.name
const ProjectName = 'ZMark'


const output = isDev ? { file: `build/${prjName}.js` } : { file: `build/${prjName}.js` };

export default {
  input: 'src/index.js',
  output: {
    ...output,
    format: 'umd',
    name: ProjectName,
    sourcemap: true
  },
  plugins: [
    nodeResolve(),
    commonjs({
      sourceMap: false,  // Default: true
    }),
    filesize(),
    babel({
      exclude: 'node_modules/**', // 只编译我们的源代码
      babelHelpers: 'runtime',
    }),
    terser(),
  ],
}
```

其中主要配置是   `input` , `output` , `plugins`

`input` 就是整个库的入口文件

`output` 就是打包后文件存放的位置，其中比较重要的是 `format` 这个参数，配置打包模块方式，常用的有 `cjs -- commonjs`  `umd -- 通用模块定义规范`  `esm -- es moudle`  ， 详情参考网上关于  **CommonJS、AMD、CMD、UMD和ES Modules的区别**

`plugins` 是打包过程中使用到的插件，rollup 的精髓就是通过各种插件实现想要的效果。

上面配置中用到如下插件：

* nodeResolve    加载node_moudles 三方包
* commonjs   （rollup 支持es模块规范，需要将commonjs转成 esm）
* filesize （构建时提示）
* babel （重要！  将 es2015+ 语法转成es5 支持的语法，相关介绍后期单独开一个文章）
* terser (打包压缩)

接着我们在 `package.json` 中添加构建脚本 

```json
"build": "rimraf ./build/ && rollup -c --pub",
```

其中 `rimraf` 是安装的插件，实现构建前将 /build 文件夹及其下面所有文件删除的功能，通过以下方式安装:

```sh
npm i rimraf -D
```

执行后就会在根目录 /build 文件下生成打包后的 js 文件

![image-20210430110115587](https://tva1.sinaimg.cn/large/008i3skNly1gq1l85x31jj30gu05omxp.jpg)





## 3. npm 发布

经过 编写库代码、rollup 构建打包，接下来就是将打包好的库发布到 npm 仓库上去。

1. 首先需要注册一个 npm 的账户
2. 完善 `package.json`  下的包信息
3. 使用 `npm login`  本地登陆账号 （注： 如果更改了 npm 源可以能遇到问题，具体参考网上解答或者换回原本的源）
4. 接着就可以直接发布了 `npm publish` ，然后在 npm 上就可以看到自己发布的包了。



## 4. 自动化发布（待续）……

整个打包发库过程还可以更自动化一点，可以通过 travis CI 结合 github来实现 构建发布一条龙服务，具体配置和使用，后面再结合 rollup 打包的 babel 配置一起来说。