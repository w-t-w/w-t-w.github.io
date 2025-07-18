---
title: think of webpack
date: 2022-08-30 16:17:26
tags: [webpack]
categories: webpack
---

# webpack

#### webpack-cli

> 作用

  - 引入 yargs,对命令行实行定制
  - 分析命令行参数,对各个参数实行转换,组成编译配置项
  - 导入 webpack,根据编译配置项进行编译和构建

#### entry

> 作用

  作为 webpack 构建打包的入口,搜索全局资源的起点.

> 属性值

  为何设置 entry 的属性值必须为相对路径,而绝对路径却会报错?

  解: entry 是 webpack 构建打包的入口,是搜索全局资源的起点,它的属性值是设置为相对于整个项目而言的,也就是当前项目根目录,在 webpack context 属性不变的情况下, entry 属性值永远相对于当前项目根目录,当然如若 context 属性值发生改变,entry 是可以设置绝对路径的,因为 webpack 构建打包所作用的项目根目录发生了改变,当然也可以强制设置 entry 属性值为绝对路径,但是其只是相对于当前设备目录而言,是存在很大的配置风险的.

#### output

> filename

  output.filename 的作用是什么?

  解: 作为 webpack 构建打包导出文件的名称.  

> path

  output.path 的作用是什么?

  解: 作为 webpack 构建打包文件导出的位置目录.

> publicPath

  output.publicPath 的作用是什么?

  解: 作为 webpack 构建打包文件导出的位置目录前缀.

> chunkFilename

  output.chunkFilename 的作用是什么?

  解: 作为 webpack 构建打包导出模块的名称.

> crossOriginLoading

  output.crossOriginLoading 的作用是什么?

  解: 其只在 target: 'web' 时生效,利用 JSONP 向文件内联 \<script\> 标签,实现按需加载模块.

#### watch

> 原理分析

  webpack --watch 文件监听的原理是什么?

  解: 轮询可监听的编辑文件的最后修改时间是否发生变化,如果发生了变化,则会引起 webpack 重新构建打包.

> 参数

  webpack --watch 文件监听有哪些参数?

  解: webpack --watch 文件监听有一些参数,全都在属性 watchOptions 中,其中包含三个主要参数: ignored、aggregateTimeout 和 poll.

  webpack --watch 文件监听里主要参数的作用是什么?

  - ignored => 筛选不实行监听的位置目录.
  - aggregateTimeout => 当轮询到可监听的编辑文件的最后修改时间发生了变化,不会立即告知监听者引起 webpack 重新构建打包,而是会加入至缓存中,在 aggregateTimeout 时间段内重复此类操作,等时间段结束之后,将缓存中的文件列表统一实行构建打包.
  - poll => 在某一个时间段内,轮询的可监听的编辑文件的最后修改时间是否发生变化的次数.

#### devtool

> sourceMap

  sourceMap 的作用是什么?

  解: 用于前端开发者根据 sourceMap 文件精确探寻源代码的一些问题,开发环境一般开启,生产环境则必须关闭.

> 参数

  首先了解几个概念: cheap、eval、inline、module、source-map

  - cheap: 构建打包的 sourceMap 文件只能通过行追踪.
  - eval: 表达式通过 eval 语法包裹,注释紧跟 eval 语法备注所出于的源代码位置目录.
  - inline: sourceMap 通过内联 sourceMap dataURI 至构建打包文件,不会单独导出 .map 文件.
  - module: 会导出未被 loader 处理的源代码.
  - source-map: 构建打包会额外单独导出 .map 文件.

  这几个概念按照逻辑排列组合,自然形成了各种参数.举几个🌰:

  - source-map: 会额外单独导出 .map 文件,可直接精确探寻源代码.
  - cheap-source-map: 会额外单独导出 .map 文件,可直接精确探寻源代码,但堆栈追踪错误只能通过行,不会显示列.
  - inline-source-map: 不会额外单独导出 .map 文件,会通过内联 sourceMap dataURI 至构建打包文件(体积会明显增大),可直接精确探寻源代码.
  - cheap-module-source-map: 会额外单独导出 .map文件,可直接精确探寻未被 loader 处理时的源代码,但堆栈追踪错误只能通过行,不会显示列.
  - eval: 不会额外单独导出 .map 文件,表达式通过 eval 语法包裹,注释紧跟 eval 语法备注所出于的源代码位置目录,无法直接精确探寻源代码.

#### splitChunks

> 参数

  - chunks: 有三个枚举选项,"async"、"initial "以及 "all",默认为 "async",表示对于异步、非异步以及全部的模块代码分割的选择.
  - minChunks: 表示模块最少可被导入实现分割的次数,默认为 1.
  - minSize: 表示模块最少可被分割的体积大小,默认为 30000,也就是 30kb.
  - maxInitialRequests: 表示入口模块并发请求的最大次数,默认为 3.
  - maxAsyncRequests: 表示模块按需加载并发请求的最大次数,默认为 5.
  - name: 模块代码分割后导出模块的名称.
  - cacheGroups: 缓存组可配置多个组,组中的元素会覆盖或者继承组外的属性值,除了 test、priority 以及 reuseExistingChunk.
  - cacheGroups.test: 筛选要分割导出的模块.
  - cacheGroups.priority: 可通过优先级来改变构建打包模块代码分割的顺序,默认值为 -20.

#### TreeShaking

> 作用

  可用于将永远执行不到的、无用的变量以及执行的代码结果不会被使用的表达式,剔除出构建打包文件.
  PS: 只能作用于可静态分析的 ESM 模块, commonjs 或者 commonjs2 模块都不会生效.

#### Scope Hoisting

> 作用

  对于不开启 Scope Hoisting 的构建打包文件来说,会存在大量的 IIFE 自执行函数表达式闭包,webpack 会对每个导入模块外加一层包裹,且将 import 转化成 __webpack_require__,这样就会导致构建打包文件体积增大,而大量的闭包也会导致设备的内存空间吃紧,而 Scope Hoisting 就是用来解决此类问题的,Webpack 4.x 当中只要将 mode 设置为 "production",就会开启 Scope Hoisting,而在 Webpack 4.x 之前的版本则需引入 new webpack.optimize.ModuleConcatenationPlugin(),Scope Hoisting 会将导入的模块内联进一个大的函数作用域中,使模块按照引用的顺序进行排列,适当的重命名一些变量以防止变量名冲突,这样就会大大减少文件以及内存空间的体积.

  PS: 只能作用于可静态分析的 ESM 模块, commonjs 或者 commonjs2 模块都不会生效.

#### resolve

> alias

  resolve.alias 的作用是什么?

  解: 使用别名替代复杂的模块导入路径.

> mainFields

  resolve.mainFields 的作用是什么?

  解: 自定义依赖被导入的文件位置.

> extensions

  resolve.extensions 的作用是什么?

  解: 选择省略导入模块的后缀.

> modules

  resolve.modules 的作用是什么?

  解: 指定导入依赖的位置目录.

> enforceExtensions

  resolve.enforceExtensions 的作用是什么?

  解: 强制导入模块必须添加后缀.

#### module

> noParse

  module.noParse 的作用是什么?

  解: 用于 webpack 筛选可忽略的未使用标准模块的依赖,如 jQuery 等,PS: 筛选可忽略的依赖应该不包含 import,define,require 等标准模块语义的语句.

#### externals

> 作用

  用于 webpack 筛选不实行构建打包的依赖,比如 jQuery 这种体积非常庞大,构建打包没有任何收益的依赖,最后直接作为全局变量内联入构建打包文件内.

#### loaders

> babel-loader

  @babel/preset-env 的参数 modules 的作用是什么?

  解: modules => 通过 babel 转化的模块导出是否为其他类型,默认模块导出为 'EsModule',其他类型如 'commonjs','commonjs2','amd' 等,设置为 false 表明不改变模块导出类型.

  @babel/preset-env 的参数 loose 的作用是什么?

  解: loose => 通过 babel 转化是否开启松散模式.

  - 何为 loose 松散模式?

    解: babel 在转换时一般有两种转换模式: 松散模式(loose)与标准模式(normal),松散模式会转换为前端开发者最熟悉的简洁的 ES5 代码,而标准模式转换出的代码则更贴近 ES6 的语义.

  - loose 松散模式的优缺点?

    - 优点: 转换出的代码更加易读,体积更小,执行效率更高,对于老旧浏览器兼容性更好.
    - 缺点: 对于原生 ES6 语法的代码转换时经常会出现问题.
    - 总结: 基本上在使用时不会开启 loose 松散模式.
  
  @babel/preset-env 的参数 useBuiltIns 的作用是什么?

  解: 是用于根据 browserslist 的浏览器版本集合来兼容的动态 polyfill 策略.

  - 'entry'

    会将 browserslist 的所有浏览器版本不兼容的 polyfill 优先全部导入至 chunks 入口处.配合 core-js@3.x 可实现按需加载,可自定义导入至入口的 polyfill 模块,PS: 必须是在 browserslist 所有浏览器版本不兼容的 polyfill 范围内.

  - 'usage'

    会将 browserslist 的所有浏览器版本不兼容的 polyfill 根据模块使用处的 API 特定按需导入.

  - false(极不推荐)

    会无视 browserslist 将所有的 @babel/polyfill 导入至 chunks 入口处.

  @babel/preset-env 的参数 corejs 的作用是什么?

  解: 必须配合 useBuiltIns: 'entry' 或者 useBuiltIns: 'usage' 使用,基本上用于将未纳入 ECMASCRIPT 稳定语法的 proposal 新特性注入至 polyfill 中.

  ```javascript
  module.exports = {
    //...
    presets: [[
        '@babel/preset-env', {
            useBuiltIns: 'usage',
            corejs: {
                version: 3,
                proposal: true
            }
        }
    ]]
    //...
  };
  ```

> css-loader

  为何 css-loader options 参数 minimize 压缩功能失效?

  解: 通过查询,webpack 3.x => 4.x && css-loader 0.x => 1.x 已经将 options minimize 参数压缩功能删掉.

> postcss-loader

  为何选用 postcss-preset-env 替代 autoprefixer 来编译转化样式文件兼容浏览器呢?

  解: postcss 本质的作用就是将更多 css 的新样式、新特性兼容更多的浏览器. postcss-preset-env 中引用了 autoprefixer,且其比 autoprefixer 能编译转化 css 的新特性,这是 autoprefixer 中不存在的功能,如 color: #12345678 这样包括透明度的十六进制 css 颜色新特性,所以选用 postcss-preset-env 替代 autoprefixer 来编译转化样式文件.

  为何 postcss.config.js > plugins 与 postcss-loader > postcssOptions > plugins 同时存在的情况下,两者同时生效?

  解: 两者同时存在的情况下,postcss.config.js > plugins 不会被覆盖,而是会被继承合并同时生效.

  现在移动端分辨率适配使用的是 vw/vh 的策略,那么如何将 UI 图上的绝对像素值( px )动态转化为 vw 呢?

  首先要了解几个概念: 
  
  - vw/vh: 类似于百分比,不过是相对于理想视口设置的宽度以及高度.
  - 理想视口: 在不实行缩放的情况下,将物理像素精确转化为各个设备的 CSS 像素(dpr)的环境.
  - DPR: DPR = 物理像素/分辨率.

  使用 postcss-px-to-viewport 来将 px 动态转化为 vw.

  ```javascript
  //postcss.config.js
  const postcssConfig = {
      plugins: [
          'postcss-preset-env',
          [
              'postcss-px-to-viewport',
              {
                  unitToConvert: 'px',
                  unitPrecision: 8,
                  viewportUnit: 'vw',
                  viewportWidth: 750
              }
          ]
      ]
  };
  module.exports = postcssConfig;
  ```

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

> thread-loader

  thread-loader 的原理是什么?

  解: webpack 每一次解析 loader 模块时,thread-loader 都会将它以及它所依赖的包分配给所对应的 worker 线程.

> loader-runner

  loader-runner 的作用是什么?

  解: 是用于测试自定义的 loader 而存在的,可以使用 loader-utils@2.x 中的 getOptions(this) 获取 loader > options,由于不是在真实的 webpack 环境下,不可使用 this.getOptions(获取 loader -> options),this.emitFile(生成文件) 等 API,PS: getOptions(this) 在 loader-utils@3.x 中已经被剔除,在自定义的 loader 中直接使用 this.getOptions() 即可.

  ```javascript
  const {readFile} = require('fs');
  const {resolve} = require('path');
  const {runLoaders} = require('loader-runner');
  
  runLoaders({
      resource: resolve(process.cwd(), './src/styles/index.less'),
      context: {minimize: true},
      loaders: [{
          loader: resolve(process.cwd(), './lib/loaders/sprite_loader.js'),
          options: {
              name: 'sprite',
              path: 'src/styles'
          }
      }],
      readResource: readFile.bind(this)
  }, (err, stats) => {
      if(err) {
          throw new Error(`测试自定义 loader 出现错误: ${err}`);
      }
      console.log(stats);
  });
  ```

#### devServer

> contentBase

  为何在 devServer 设置 contentBase 失效?

  解: contentBase 指的是 devServer 本地代理服务的作用目录,一般设置为绝对路径,在 webpack 5.x 中改为 static,webpack 4.x 及其之前版本依然生效.

> historyApiFallback

  devServer.historyApiFallback 的作用是什么?

  解: 作为 devServer 本地代理服务指定遇到 404 或者错误时重定向的页面.

> allowedHosts

  devServer.allowedHosts 的作用是什么?

  解: 作为 devServer 本地代理服务页面访问时 http 请求的白名单.

> https

  devServer.https 的作用是什么?

  解: 作为 devServer 本地代理服务是否开启 https 安全协议.

> compress

  devServer.compress 的作用是什么?

  解: 作为 devServer 本地代理服务是否开启 Gzip 压缩.

> open

  devServer.open 的作用是什么?

  解: 作为 devServer 本地代理服务首次构建打包时,是否自动打开设备默认浏览器实行访问.

> hot

  热加载的工作原理是什么?

  解: 首先要了解几个概念: webpack compile、bundle server、hmr server 以及 hmr runtime.

  - webpack compile: 是将 js 转化成 bundleJs 的编译器,同时开启 webpack --watch 文件监听,并写入内存.
  - bundle server: 提供文件给浏览器实行访问.
  - hmr server: 将 Server 端热加载文件输出给 Client 端 hmr runtime.
  - hmr runtime: 注入至浏览器内存中;接受 hmr server 输出的热加载文件并实行更新.

  工作原理: 首次编译,webpack compile 将 js 编译为 bundleJs,同时开启 webpack --watch 文件监听并写入内存,通过 bundler server 提供文件给浏览器实行访问,与此并行的是 hmr runtime 注入至浏览器内存中;接着文件监听轮询可监听的编辑文件是否发生变化,如果发生了变化,不会立即告知监听者,引起 webpack 重新构建打包,而是会放入至缓存中,在 aggregateTimeout 时间段内重复此类操作,等时间段结束之后,将缓存中的文件列表统一重新构建打包,之后通过 hmr server 将热加载文件输出给 hmr runtime,其中的文件传输协议为 websocket,传输数据格式为 JSON 格式,hmr runtime 接收到 hmr server 热加载文件实行更新.

#### plugins

> friendly-errors-webpack-plugin

  为何 friendly-errors-webpack-plugin 日志优化插件在 webpack 5.x 中不生效?

  解: friendly-errors-webpack-plugin 日志优化插件目前在 github 上已停止维护,其只能生效的 webpack 版本为 4.x 以及之前的版本,对于 webpack 5.x 无法下载使用.

> clean-webpack-plugin

  如何选择性清空构建打包导出的位置目录内容?

  解: clean-webpack-plugin 存在一个参数属性 —— cleanOnceBeforeBuildPatterns.
 
  - cleanOnceBeforeBuildPatterns: 用于筛选在 Webpack Compile 编译前实行清空的位置目录内容.

  用此属性就可选择性清空构建打包导出的位置目录内容.

> image-minimizer-webpack-plugin

  image-minimizer-webpack-plugin 图片资源模块压缩功能插件有什么作用?

  解: 是用于将所有类型的图片资源模块利用 imagemin 的方式优化压缩打包功能的最佳处理方式.

  ```javascript
  const ImageMinimizerWebpackPlugin = require('image-minimizer-webpack-plugin');
  module.exports = {
    //...
    optimization: {
        minimizer: [
            //...
            new ImageMinimizerWebpackPlugin({
                minimizer: {
                    implementation: ImageMinimizerWebpackPlugin.imageminMinify,
                    options: {
                        plugins: [
                            ["gifsicle", { interlaced: true }],
                            ["jpegtran", { progressive: true }],
                            ["optipng", { optimizationLevel: 5 }],
                            [
                                "svgo",
                                {
                                    plugins: [
                                        {
                                            name: "preset-default",
                                            params: {
                                                overrides: {
                                                    removeViewBox: false,
                                                    addAttributesToSVGElement: {
                                                        params: {
                                                            attributes: [
                                                                { xmlns: "http://www.w3.org/2000/svg" },
                                                            ],
                                                        },
                                                    },
                                                },
                                            },
                                        },
                                    ],
                                },
                            ],
                        ]
                    }
                }
            }),
            //...
        ]
    }
    //...
  };
  ```

#### commit rules

> 规范说明

  现阶段大多数前端开发者遵从的 git commit message 规范都是 Angular 团队的 Angular commit message.

  ```text
  <type>(<scope>): <subject>  <!-- 这一行为 header -->
  <breakLine>
  <body>    
  <breakLine>
  <footer>
  ```

  - header: 分为三部分,type、scope 以及 subject.
    - type: 表示提交类型.必填且必须为以下枚举之一: \['build', 'chore', 'feat', 'fix', 'style', 'perf', 'docs', 'ci', 'test', 'refactor'\].
    - scope: 表示项目内文件修改的范围,比如说 fix 修复 bug 时,选择 hooks 部分还是 component 部分,选填.
    - subject: 表示简要描述本次提交.必须遵循两个规则: 首字母不能大写,末尾不能添加 '.' 字符,必填.
  - body: 表示对本次提交的详细描述,选填.
  - footer: 分为两种情况,BREAKING CHANGES 以及 删除 issue,选填.
    - BREAKING CHANGES: 与当前 API 产生了较大的不兼容时,如重构,会详细描述本次提交的 BREAKING CHANGES,必须以 BREAKING CHANGES 开头.
    - delete issue: 当需要删除本次 commit 所对应的 issue 时,也可详细描述.
  
> type enum

  - Angular commit message type.
    - build: 初始化打包.
    - chore: 构建/依赖/工具.
    - feat: 新功能.
    - fix: 修复 Bug.
    - style: 代码样式美化.
    - perf: 性能优化.
    - docs: 文档变更.
    - test: 测试.
    - ci: CI related changes.
    - refactor: 重构.
  
  - gitmoji commit message type.
    - 遵从且继承 Angular commit message type.
    - revert: 回滚.
    - wip: 建设进程中(不推荐使用).

> 规范化工具

  通常使用 commitizen + cz-customizable 来实现 gitmoji commit message 规范化.

  ```json
  {
    "config": {
      "commitizen": {
         "paths": "node_modules/cz-customizable"
      }
    }
  }
  ```

  ```javascript
  //.cz-config.js
  const czConfig = {
      types: [{
          name: '🍻 build: 初始化打包',
          value: ':beers: build'
      }, {
          name: '📦️chore: 构建/依赖/工具',
          value: ':package: chore'
      }, {
          name: '✨  feat: 新功能',
          value: ':sparkles: feat'
      }, {
          name: '🐛 fix: 修复bug',
          value: ':bug: fix'
      }, {
          name: '🎨 style: 代码样式优化',
          value: ':art: style'
      }, {
          name: '📄 docs: 变更文档',
          value: ':page_facing_up: docs'
      }, {
          name: '🚀 perf: 性能优化',
          value: ':rocket: perf'
      }, {
          name: '✅  test: 测试',
          value: ':white_check_mark: test'
      }, {
          name: '🔥 refactor: 重构',
          value: ':fire: refactor'
      }, {
          name: '👷 ci: CI related changes',
          value: ':construction_worker: ci'
      }],
      messages: {
          type: '请输入您本次提交类型(必填):',
          scope: '请输入您本次提交修改范围:',
          customScope: '请选择您本次提交修改范围:',
          subject: '请简要描述本次提交(必填):',
          body: '请对本次提交作详细描述:',
          breaking: '请对本次提交与当前 API 产生比较大的不兼容作详细描述:',
          footer: '请对本次提交删除的所对应的 issue 作详细描述:',
          confirmCommit: '是否确认提交以上选择输入?'
      },
      scopes: [{name: 'components       [组件部分]'}, {name: 'hooks            [hooks部分]'}, {name: 'logics           [代码逻辑部分]'}],
      allowCustomScopes: true,
      allowEmptyScopes: true,
      customScopesName: 'custom           [自定义]',
      emptyScopesName: 'empty            [不指定]',
      allowBreakingChanges: [':sparkles: feat', ':bug: fix'],
      subjectLimit: 80
  };
  ```

> 强制校验 gitmoji commit message

  通常使用 commitlint + husky + commitlint-config-gitmoji 来强制校验 git 提交时的 gitmoji commit message 规范.

  ```javascript
  // .commitlintrc.js
  const commitlintrcConfig = {
    extends: ['gitmoji'],
  };
  module.exports = commitlintrcConfig;
  ```

  ```shell
  # husky
  # 生成强制校验 git 操作的 hooks 容器.
  npx husky install
  # 添加强制校验 gitmoji commit message 规范的 hooks,并写入默认命令.
  npx husky add .husky/commet-msg "yarn run test"
  ```

  ```shell
  # husky
  #!/usr/bin/env sh
  . "$(dirname -- "$0")/_/husky.sh"
  # commitlint --edit 是对 git 最后一次本地提交进行强制校验,如若校验不通过则实行回退,本地提交不成功.
  npx --no-install commitlint --edit "$1"
  ```

> 强制生成 CHANGELOG

  通常使用 husky + standard-version + conventional-changelog-gitmoji-config 来强制生成 CHANGELOG.md 文件.

  ```shell
  # 添加强制校验 gitmoji commit message 规范的 hooks,并写入默认命令.
  npx husky add .husky/pre-push "yarn run test"
  ```

  ```shell
  # husky
  #!/usr/bin/env sh
  . "$(dirname -- "$0")/_/husky.sh"
  # 生成 CHANGELOG.md 文件的规则遵循 conventional-changelog-gitmoji-config,不可进行自定义.
  npx --no-install standard-version --preset gitmoji-config
  ```

#### speed optimization

> best answer

  实验证明, webpack 5.x 中使用 splitChunks 分割自定义公用模块,使用 DllPlugin + DllReferencePlugin 抽取公用依赖至 manifest.json 文件内做预编译文件,只需要构建打包一次,后续则不需要重新执行,可由此看出是速度优化的最佳答案.
    
> BREAKING CHANGES

在 webpack 4.x 中做了一些速度优化升级:

- nodejs 必须下载 v8.5.0 以上的稳定版本才可使用,而新版本的 V8 带来了一些语法优化.
  - for-of 替代 forEach.
  - Map、Set 替代 Object.
  - includes 替代 indexOf.
- 使用更快的 md4 hash 加密算法.
- webpack AST 可以直接通过 loader 传递给 AST,减少解析时间,解析速度更快.
- 使用字符串方法替代正则表达式,运行速度更快.

  
