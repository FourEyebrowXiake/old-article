---
title: webpack基础
date: 2017-08-11 22:33:24
tags: Webpack
categories: 前端开发
---
# webpack
## 模块化开发
webpack本身就是一款用户打包前端模块的工具。主要是用来打包在浏览器端使用的javascript的。同时也能转换、捆绑、打包其他的静态资源，包括css、image、font file、template等

要了解它，首先要知道什么叫模块化开发。

前端开发中，起初只要在script标签中嵌入几十上百行代码就能实现一些基本的交互效果，后来js得到重视，应用也广泛起来了，jQuery，Ajax，Node.Js，MVC，MVVM等的助力也使得前端开发得到重视，也使得前端项目越来越复杂，然而，JavaScript却没有为组织代码提供任何明显帮助，甚至没有类的概念，更不用说模块了。但javascript可以实现模块，一个定义了私有变量和函数的函数。

什么鬼，什么叫定义了私有变量和函数的函数？不知道的同学，可以去看一下《javascript语言精粹》的模块章节。

简单地讲，一个模块就是实现特定功能的文件，有了模块，我们就可以更方便地使用别人的代码，想要什么功能，就加载什么模块。模块开发需要遵循一定的规范，否则就都乱套了。

这就是模块化开发。而webpack能将依赖的模块转化成可以代表这些包的静态文件。

<!-- more -->

## Webpack它的目标是什么？它和其他类似的工具有什么不同？webpack为什么要将所有资源放在一个文件夹里？
### [详情请见《webpack学习之路》](https://github.com/wangning0/Autumn_Ning_Blog/blob/master/blogs/3-12/webpack.md)

---

## webpack.config.js详解
看代码说话：
```javascript
var webpack = require('webpack');
var path = require('path');
var config_webpath_dev = require('./configs/webpack_dev');

var config = {
    resolve: {
        alias:{
            'react':path.join(nodeModulesPath,'react/react.js')
        }
			},
		entry: ['./src/index'],
	  output: {
	    path: path.join(__dirname, 'dist'),
	    filename: 'bundle.js'
	  },
	  plugins: [
	    new webpack.optimize.UglifyJsPlugin({
	      compressor: {
	        warnings: false,
	      },
	    }),
	    new webpack.optimize.OccurenceOrderPlugin()
	  ],
	  module: {
	    loaders: [{
	      test: /\.css$/,
	      loaders: ['style', 'css']
	    }]
	  },
    devServer: {
        contentBase: "./public",
        colors: true,
        historyApiFallback: true,
        inline: true,
        hot: true,
        host: config_webpath_dev.domain,
        port: config_webpath_dev.port
    },
}
module.exports = config;
```

### resolve 这些选项能设置模块如何被解析
alias配置项，可以为常用模块配置改属性，可以节省编译的搜索时间。例如：
```javascript
resolve:{
			 alias:{
					 'react':path.join(nodeModulesPath,'react/react.js')
			 }
	 }

```
### entry	是指入口文件的配置项，它是一个数组的原因是webpack允许多个入口点。

### output是指输出文件的配置项
-  path - 表示输出文件的路径
- filename - 表示输出文件的文件名


### plugins 顾名思义，使用插件可以给webpack添加更多的功能，使webpack更加的灵活和强大,webpack有两种类型的插件:

####  webpack内置的插件

```javascript
  // 首先要先安装webpack模块
  var webpack = require("webpack");

  module.exports = {
      new webpack.optimize.UglifyJsPlugin({
        compressor: {
          warnings: false,
        },
  	})h
  };
```
####  webpack外置插件

```javascript
  //npm install component-webpack-plugin 先要在安装该模版
  var ComponentPlugin = require("component-webpack-plugin");
  module.exports = {
      plugins: [
          new ComponentPlugin()
      ]
  }
```
更多的插件以及插件的用法，大家可以到webpack的插件上查看。

### module 决定了如何处理项目中的不同类型的模块。它通过配置要使用的loader，把资源文件（css、图片、html等非js模块）处理成相应的js模块,然后其它的plugins才能对这些资源进行下一步处理。比如babel-loader可以把es6的文件转换成es5。


#### loaders 一个含有wepback中能处理不同文件的加载器的数组

- test 用来匹配相对应文件的正则表达式
- loaders 告诉webpack要利用哪种加载器来处理test所匹配的文件
#### loaders 的安装方法
```javascript
  $ npm install xxx-loader --save-dev
```
### devServer(webpack-dev-server) 能够用于快速开发应用程序
- color - Enables/Disables colors on the console.
- contentBase - 告诉服务器从哪里提供内容。只有在你想要提供静态文件时才需要。devServer.publicPath 将用于确定应该从哪里提供 bundle，并且此选项优先。

- historyApiFallback - 当使用 HTML5 History API 时，任意的 404 响应都可能需要被替代为 index.html.
- hot - 启用 webpack 的模块热替换特性.
- inline - 默认情况下，应用程序启用内联模式(inline mode)。这意味着一段处理实时重载的脚本被插入到你的包(bundle)中，并且构建消息将会出现在浏览器控制台。

- host - 指定使用一个 host。默认是 localhost。如果你希望服务器外部可访问，指定如下：

- port - 指定要监听请求的端口号
