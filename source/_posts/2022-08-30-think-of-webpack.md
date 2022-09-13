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

#### watch

> 原理分析

  webpack --watch 文件监听的原理是什么?

  解: 轮询可监听的编辑文件的最后修改时间是否发生变化,如果发生了变化,则会引起 webpack 重新构建打包.

> 参数

  webpack --watch 文件监听有哪些参数?

  解: webpack --watch 文件监听有一些参数,全都在属性 watchOptions 中,其中包含三个主要参数: ignored、aggregateTimeout和poll.

  webpack --watch 文件监听里主要参数的作用是什么?

  - ignored => 筛选不实行监听的位置目录.
  - aggregateTimeout => 当轮询到可监听的编辑文件的最后修改时间发生了变化,不会立即告知监听者引起 webpack 重新构建打包,而是会加入至缓存中,在 aggregateTimeout 时间段内重复此类操作,等时间段结束之后,将缓存中的文件列表统一实行构建打包.
  - poll => 在某一个时间段内,轮询的可监听的编辑文件的最后修改时间是否发生变化的次数.

#### devtool

> sourceMap

  sourceMap 的作用是什么?

  解: 用于前端开发者根据 sourceMap 文件探寻源代码的一些问题,开发环境一般开启,生产环境则必须关闭.

> 参数

  首先了解几个概念: cheap、eval、inline、module、source-map

  - cheap: 构建打包的 sourceMap 文件只能通过行追踪.
  - eval: 表达式通过 eval 语法包裹,注释紧跟 eval 语法备注所出于的源代码位置目录.
  - inline: sourceMap 通过内联 sourceMap dataURI 至构建打包文件,不会单独导出 .map 文件.
  - module: 会导出未被 loader 处理的源代码.
  - source-map: 构建打包会额外单独导出 .map 文件.

  这几个概念按照逻辑排列组合,自然形成了各种参数.举几个🌰:

  - source-map: 会额外单独导出 .map 文件,可直接探寻源代码.
  - cheap-source-map: 会额外单独导出 .map 文件,可直接探寻源代码,但堆栈追踪错误只能通过行,不会显示列.
  - inline-source-map: 不会单独导出 .map 文件,会通过内联 sourceMap dataURI 至构建打包文件(体积会明显增大),可直接探寻源代码.
  - cheap-module-source-map: 会额外单独导出 .map文件,可直接探寻未被 loader 处理时的源代码,但堆栈追踪错误只能通过行,不会显示列.

#### loaders

> babel-loader

  @babel/preset-env 的参数 modules 的作用是什么?

  解: modules => 通过 babel 转化的模块导出是否为其他类型,默认模块导出为 'EsModule',其他类型如 'commonjs','commonjs2','amd' 等,设置为 false 表明不改变模块导出类型.

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

> postcss-loader

  为何选用 postcss-preset-env 替代 autoprefixer 来编译转化样式文件兼容浏览器呢?

  解: postcss 本质的作用就是将更多 css 的新样式、新特性兼容更多的浏览器.postcss-preset-env 中引用了 autoprefixer,且其比 autoprefixer 能编译转化 css 的新特性,这是 autoprefixer 中不存在的功能,如 color: #12345678 这样包括透明度的十六进制 css 颜色新特性,所以选用 postcss-preset-env 替代 autoprefixer 来编译转化样式文件.

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

> raw-loader

  在之前的移动端分辨率适配时,是使用相对像素值(rem)+动态计算元节点绝对像素值的策略,但是却会存在 raw-loader 内联资源必须下载低版本 0.5.1 的限制,为什么必须要下载低版本 0.5.1 的 raw-loader 呢?

  解: 首先了解几个概念: px2rem-loader、lib-flexible 以及 raw-loader.

  - px2rem-loader: 用于将 px 绝对像素值转化为 rem 相对像素值.
  - lib-flexible: 用于动态计算元节点绝对像素值的工具.
  - raw-loader: 用于内联资源模块,导出文件资源的源代码.

  新版本的 raw-loader 存在一些问题,查询了源代码后发现,导出的资源模块源代码模块导出类型为 'EsModule',转化成字符串为'\[object Object\]',要对 raw-loader 进行处理,对其属性 esModule 设置为 false,就可解决必须下载低版本 0.5.1 的 raw-loader 的限制.

  ```ejs
  <!-- 使用 raw-loader 内联资源模块之前  -->
  <!DOCTYPE html>
    <html lang="zh-CN">
    <head>
        <meta charset="UTF-8">
        <title>Webpack Emulate</title>
        <script type="text/javascript"><%= require("raw-loader?esModule=false!babel-loader!../node_modules/lib-flexible/flexible") %></script>
    </head>
    <body>
    <div id="root-webpack">
    
    </div>
    </body>
  </html>
  ```

  ```ejs
  <!-- 使用 raw-loader 内联资源模块之后  -->
  <!doctype html><html lang="zh-CN"><head><meta charset="UTF-8"><title>Webpack Emulate</title>
    <script>;(function (win, lib) {
            var doc = win.document;
            var docEl = doc.documentElement;
            var metaEl = doc.querySelector('meta[name="viewport"]');
            var flexibleEl = doc.querySelector('meta[name="flexible"]');
            var dpr = 0;
            var scale = 0;
            var tid;
            var flexible = lib.flexible || (lib.flexible = {});

            if (metaEl) {
                console.warn('将根据已有的meta标签来设置缩放比例');
                var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);

                if (match) {
                    scale = parseFloat(match[1]);
                    dpr = parseInt(1 / scale);
                }
            } else if (flexibleEl) {
                var content = flexibleEl.getAttribute('content');

                if (content) {
                    var initialDpr = content.match(/initial\-dpr=([\d\.]+)/);
                    var maximumDpr = content.match(/maximum\-dpr=([\d\.]+)/);

                    if (initialDpr) {
                        dpr = parseFloat(initialDpr[1]);
                        scale = parseFloat((1 / dpr).toFixed(2));
                    }

                    if (maximumDpr) {
                        dpr = parseFloat(maximumDpr[1]);
                        scale = parseFloat((1 / dpr).toFixed(2));
                    }
                }
            }

            if (!dpr && !scale) {
                var isAndroid = win.navigator.appVersion.match(/android/gi);
                var isIPhone = win.navigator.appVersion.match(/iphone/gi);
                var devicePixelRatio = win.devicePixelRatio;

                if (isIPhone) {
                    // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
                    if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {
                        dpr = 3;
                    } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)) {
                        dpr = 2;
                    } else {
                        dpr = 1;
                    }
                } else {
                    // 其他设备下，仍旧使用1倍的方案
                    dpr = 1;
                }

                scale = 1 / dpr;
            }

            docEl.setAttribute('data-dpr', dpr);

            if (!metaEl) {
                metaEl = doc.createElement('meta');
                metaEl.setAttribute('name', 'viewport');
                metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');

                if (docEl.firstElementChild) {
                    docEl.firstElementChild.appendChild(metaEl);
                } else {
                    var wrap = doc.createElement('div');
                    wrap.appendChild(metaEl);
                    doc.write(wrap.innerHTML);
                }
            }

            function refreshRem() {
                var width = docEl.getBoundingClientRect().width;

                if (width / dpr > 540) {
                    width = 540 * dpr;
                }

                var rem = width / 10;
                docEl.style.fontSize = rem + 'px';
                flexible.rem = win.rem = rem;
            }

            win.addEventListener('resize', function () {
                clearTimeout(tid);
                tid = setTimeout(refreshRem, 300);
            }, false);
            win.addEventListener('pageshow', function (e) {
                if (e.persisted) {
                    clearTimeout(tid);
                    tid = setTimeout(refreshRem, 300);
                }
            }, false);

            if (doc.readyState === 'complete') {
                doc.body.style.fontSize = 12 * dpr + 'px';
            } else {
                doc.addEventListener('DOMContentLoaded', function (e) {
                    doc.body.style.fontSize = 12 * dpr + 'px';
                }, false);
            }

            refreshRem();
            flexible.dpr = win.dpr = dpr;
            flexible.refreshRem = refreshRem;

            flexible.rem2px = function (d) {
                var val = parseFloat(d) * this.rem;

                if (typeof d === 'string' && d.match(/rem$/)) {
                    val += 'px';
                }

                return val;
            };

            flexible.px2rem = function (d) {
                var val = parseFloat(d) / this.rem;

                if (typeof d === 'string' && d.match(/px$/)) {
                    val += 'rem';
                }

                return val;
            };
        })(window, window['lib'] || (window['lib'] = {}));</script><link href="./css/index.5c90e18c.css" rel="stylesheet"></head><body><div id="root-webpack"></div><script defer="defer" src="./js/index_7b84cb4c006925aa2404.js"></script></body></html>
  ```

#### devServer

> contentBase

  为何在 devServer 设置 contentBase 失效?

  解: contentBase 指的是 devServer 本地代理服务的作用目录,一般设置为绝对路径,在 webpack 5.x 中改为 static,webpack 4.x 及其之前版本依然生效.

> hot

  热加载的工作原理是什么?

  解: 首先要了解几个概念: webpack compile、bundle server、hmr server 以及 hmr runtime.

  - webpack compile: 是将 js 转化成 bundleJs 的编译器,同时开启 webpack --watch 文件监听,并写入内存.
  - bundle server: 提供文件给浏览器实行访问.
  - hmr server: 将 Server 端热加载文件输出给 Client 端 hmr runtime.
  - hmr runtime: 注入至浏览器内存中;接受 hmr server 输出的热加载文件并实行更新.

  工作原理: 首次编译,webpack compile 将 js 编译为 bundleJs,同时开启 webpack --watch 文件监听并写入内存,通过 bundler server 提供文件给浏览器实行访问,与此并行的是 hmr runtime 注入至浏览器内存中;接着文件监听轮询可监听的编辑文件是否发生变化,如果发生了变化,不会立即告知监听者,引起 webpack 重新构建打包,而是会放入至缓存中,在 aggregateTimeout 时间段内重复此类操作,等时间段结束之后,将缓存中的文件列表统一重新构建打包,之后通过 hmr server 将热加载文件输出给 hmr runtime,其中的文件传输协议为 websocket,传输数据格式为 JSON 格式,hmr runtime 接收到 hmr server 热加载文件实行更新.

  
