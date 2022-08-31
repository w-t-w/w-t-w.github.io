---
title: think of webpack
date: 2022-08-30 16:17:26
tags: [webpack]
categories: webpack
---

# webpack

#### entry

> 属性值

  为何设置 entry 的属性值必须为相对路径,而绝对路径却会报错?

  解: entry 是 webpack 构建打包的入口,是搜索全局资源的起点,它的属性值是设置为相对于整个项目而言的,也就是当前项目根目录,在 webpack context 属性不变的情况下, entry 属性值永远相对于当前项目根目录,当然如若 context 属性发生改变,entry 是可以设置绝对路径的,因为 webpack 所作用的构建打包项目根目录发生了改变. 

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

  解: url-loader 与 file-loader 在处理文件资源功能上面并没有很大的区别,url-loader 中是引用了 file-loader 的,但是 url-loader 根据参数限制能够转化为更小的文件资源 dataURI,比如图片 Base64.

> Asset Module

  webpack 5.x 中对于资源模块的处理进行了升级优化,无需额外配置即可处理资源文件(字体，图标等).

  资源模块类型(asset module type),通过添加 4 种新的模块类型,来替换所有这些 loader:

  - asset/resource 发送一个单独的文件并导出 URL.之前通过使用 file-loader 实现.
  - asset/inline 导出一个资源的 data URI.之前通过使用 url-loader 实现.
  - asset/source 导出资源的源代码.之前通过使用 raw-loader 实现.
  - asset 在导出一个 data URI 和发送一个单独的文件之间自动选择.之前通过使用 url-loader,并且配置资源体积限制实现.

  asset 实际上可以理解为比较通用灵活的配置,通常开发时基本上使用 type: 'asset'.

  - generator

    在配置 module.rules 时,可配置 generator 来对资源模块处理进行进一步控制,该配置有以下几个属性:

    - filename
    
      - 值必须为相对路径,可改变资源输出的目录;
      - 如果同时指定了该属性和 output.assetModuleFilename,将忽略 output.assetModuleFilename 的值;
      - 该属性仅适用于 asset 和 asset/resource 资源类型.
    
    - dataUrl

      - 在处理 inline 类型的资源时,该资源默认对资源进行 base64 编码,可通过该属性来改变其编码方式;
      - 该属性值为函数,其签名为 (content: string) => string;
      - 该属性仅适用于 asset 和 asset/inline 资源类型.
    
  - parser

    - dataUrlCondition.maxSize:

      - 当 type 为 asset 时,如果资源大小小于 8kb,按照 asset/inline 的规则处理资源,否则按照 asset/resource 的规则处理资源;
      - 可通过指定该属性来改变其界限值(单位为 byte);
      - 该属性仅适用于 asset 资源类型.
  
  示例:

  ```javascript
  module.exports = {
    //...
    module: {
        rules: [//...
        {
            test: /\.(bmp|gif|jpg|jpeg|gif)$/,
            type: 'asset',
            generator: {
                publicPath: './',
                // 注意,这里资源模块文件后缀[ext]与引入 loader 方式时有所不同,这里代表.bmp .png .gif .jpg .jpeg,而引入 loader 时,[ext] 是不含有 '.' 字符的.
                filename: 'assets/images/[name].[contenthash:6][ext]'
            },
            parser: {
                dataUrlCondition: {
                    maxSize: 5 * 1024
                }
            }
        }
        //..
        ]
    }
    //...
  };
  ```

#### devServer

> contentBase

  为何在 devServer 设置 contentBase 失效?

  解: contentBase 指的是 devServer 本地代理服务的作用目录,一般设置为绝对路径,在 webpack 5.x 中改为 static,webpack 4.x 及其之前版本依然生效.

  
