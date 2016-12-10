---
title: webpack学习笔记
date: 2016-12-09 09:48:24
tags: webpack
---

最近开始学习webpack，本篇文章中的例子主要了参考webpack官网的指南。

### 准备工作
使用webpack需要安装npm和node，这里我使用的是node v6.9.2 & npm v3.10.3

### 初始化一个项目
1.新建一个名为webpack的文件夹，创建两个目录 bin和src
``` bash
mkdir bin
mkdir src
```
2.为项目安装webpack

<!-- more -->

``` bash
npm install --save-dev webpack
```
--save 会自动把模块和版本号添加到package.json中的dependencies中，-dev会添加到devdependencies中，也就是当前的开发目录中，denpendencies是产品依赖模块，devdependencies是开发依赖模块，所以如果开发过程中需要的东西，需要-dev    
3.在项目下增加index.html, src中新建两个文件，app.js & cats.js
app.js作为整个webpack工程的entry，也是webpack进行模块管理依赖的入口
index.html中的内容
``` javascript
 <!DOCTYPE html>
 <html>
     <head>
         <meta charset="utf-8">
     </head>
     <body>
     	<div id="main"></div>
         <script src="bin/app.bundle.js" charset="utf-8"></script>
     </body>
 </html>
```
cats.js中的内容
``` javascript
let cats = ['dave', 'henry', 'martha'];
module.exports = cats;
```
app.js中的内容
``` javascipt
import 'babel-polyfill'
import cats from './cats'

let $main = document.querySelector('#main')
for (const cat of cats) {
	let childNode = document.createElement('p')
	childNode.innerText = cat
	$main.appendChild(childNode)
}
```
4.安装babel-loader    
因为使用了ES6，所以需要安装babel等依赖，
``` bash
npm install --save-dev babel-core babel-preset-es2015
npm install --save-dev babel-loader
npm install --save babel-polyfill
```
babel-polyfill是为IE8等浏览器添加完整的ES5 API
还需要在项目中添加一个.babelrc文件，添加
``` javascript
{"presets": [ "es2015" ]}
```
5.配置webpack.config.js
这个文件是webpack的灵魂，所有的配置都在这个文件中设置。
``` javascript
module.exports = {
  entry: './src/app.js',
  output: {
    path: './bin',
    filename: 'app.bundle.js'
  },
  module: {
  	loaders: [{
  		test: /\.js$/,
  		exclude: /node-modules/,
  		loader: 'babel-loader'
  	}]
  }
}
```
entry中设置了整个webpack进行依赖管理的入口文件，output是打包后生成的文件，现在设置为./bin文件夹下的app.bundle.js文件。    
使用babel-loader处理es6的写法，匹配除了/node-modules目录下的所有.js的文件
6.通过webpack指令对项目进行打包
在项目目录下运行
``` bash
webpack
```
控制台打印结果：
``` bash
$ webpack
Hash: a9e98fe2469f09c42db9
Version: webpack 1.14.0
Time: 5411ms
        Asset     Size  Chunks             Chunk Names
app.bundle.js  91.6 kB       0  [emitted]  main
    + 300 hidden modules

```
可以在./bin文件下查看到生成的app.bundle.js，点击查看index.html
![index result](http://ww4.sinaimg.cn/large/59967359gw1fakkllpcnaj207i038q2q.jpg)    
在app.bundle.js中可以看到，其实整个模块管理通过一个function __webpack_require__(moduleId) 实现，每个模块都拥有一个自己的moduleId和exports，如果依赖于某个模块，则使用__webpack_require__(moduleId)调用。entry作为入口，通过__webpack_require__(0)，而在0中，则通过__webpack_require__调用自己所依赖的模块
![entry中调用](http://ww2.sinaimg.cn/large/59967359gw1fakl0oer1oj20e906ldgt.jpg)

### loader扩展
在上面的例子中，已经使用了一个babel-loader，其实还有很多非常常用的loader，比如处理css和各种静态资源的loader
1.处理css
在项目中添加一个style.css的文件
``` javascript
body {
	background: yellow;
}
```
在app.js中引入style.css
``` javascript
import 'babel-polyfill'
import cats from './cats'
import '../style.css'
```
在webpack.config.js中配置需要的loader
``` javascript
module: {
  	loaders: [{
  		test: /\.js$/,
  		exclude: /node-modules/,
  		loader: 'babel-loader'
  	}, {
  		test: /\.css$/,
  		loader: "style!css"
  	}]
  }
```
这里注意要npm安装对应的loader
``` bash
npm install --save style-loader
npm install --save css-loader
```
