---
title: unresolved problems
date: 2022-09-20 08:53:19
tags: ['unresolved']
categories: unresolved
---
# webpack

#### speed-measure-webpack-plugin

  为何 speed-measure-webpack-plugin 速度分析插件对于 webpack 5.x 的支持很有限制,如何完美的解决这些限制?

现阶段并没有发现解决的最完美的方式, speed-measure-webpack-plugin 速度分析插件对于 webpack 5.x 会出现以下两个问题:

![](https://image.white-than-wood.zone/webpack/speed-measure-webpack-plugin-problem.png)

使用 .wrap 包裹后,会直接报 TypeError 类型错误,经过查询可知,官方给出的解决方案是,将 optimization.minimizer 的默认值去掉,也就是 '...'.
那就有疑问了,如果不合并 minimizer 默认值,对于prod 生产环境的打包速度分析时,我还需要额外下载 terser-webpack-webpack 对构建打包文件实行压缩混淆,还需要额外引入 new webpack.optimize.ModuleConcatenationPlugin 来开启 Scope Hoisting 吗?还是说要放弃 css-minimizer-webpack-plugin 压缩 css 文件,使用其他的比较麻烦的方案呢?这些方式勉强可以处理,但是都太得不偿失.
参考链接: <a href='https://github.com/stephencookdev/speed-measure-webpack-plugin/issues/171'>Not working with Webpack 5: "TypeError: Cannot create proxy with a non-object as target or handler"</a>.

![](https://image.white-than-wood.zone/webpack/speed-measure-webpack-plugin-problem-ano.png)

即使按照官方的解决方案,去掉 optimization.minimizer 的默认值 '...',也会报 "You forgot to add 'mini-css-extract-plugin' plugin" 这个问题,针对于此官方的解决方案是,先试用 speed-measure-webpack-plugin 速度分析,而后再将 mini-css-extract-plugin 抽取 css 文件插件添加到 webpack plugins 中.感觉还是好愚蠢的做法,很不优雅而且多余.
参考链接: <a href='https://github.com/stephencookdev/speed-measure-webpack-plugin/issues/167#issuecomment-976836861'>"You forgot to add 'mini-css-extract-plugin' plugin"</a>.
