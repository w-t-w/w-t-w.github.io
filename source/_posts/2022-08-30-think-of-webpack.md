---
title: think of webpack
date: 2022-08-30 16:17:26
tags: [webpack]
categories: webpack
---

# webpack

#### loaders

> babel-loader

  @babel/preset-env 的参数 modules 的作用是什么?

  解: modules => 通过 babel 转化的模块导出是否为其他类型,默认模块导出为 'EsModules',其他类型如 'commonjs','commonjs2','amd' 等,设置为 false 表明不改变模块导出类型.

  @babel/preset-env 的参数 loose 的作用是什么?

  解: loose => 通过 babel 转化是否开启松散模式.

  - 何为 loose 松散模式?

    解: babel 在转换时一般有两种转换模式: 松散模式(loose)与标准模式(normal),松散模式会转换为前端开发者最熟悉的简洁的 ES5 代码,而标准模式转换出的代码则更贴近 ES6 的语义.

  - loose 松散模式的优缺点?

    - 优点: 转换出的代码更加易读,体积更小,执行效率更高,对于老浏览器兼容性更好.
    - 缺点: 对于原生 ES6 语法的代码转换时经常会出现问题.
    - 总结: 基本上在使用时不会开启 loose 松散模式.

> css-loader

  为何 css-loader options 参数 minimize 压缩功能失效?

  解: 通过查询,webpack 3.x => 4.x && css-loader 0.x => 1.x 已经将 options minimize 参数压缩功能删掉.

> url-loader

  url-loader 与 file-loader 的区别是什么?

  解: url-loader 与 file-loader 在处理文件资源功能上面并没有很大的区别,url-loader 中是引用了 file-loader 的,但是 url-loader 根据参数限制能够转化为更小的文件资源,比如图片 Base64.

  
