---
title: daily problems
date: 2022-08-30 18:53:14
tags: [problems]
categories: problems
---

# npm

> npm run scripts

  为何 npm 执行 package.json 中的 scripts 命令时,即使全局环境变量下不存在的脚本命令也能执行?
  
  解: 首先 npm run package.json 中的 scripts 脚本命令时,会新建一个 shell 脚本,并将 scripts 中的脚本命令放入其中执行,因此 shell 命令(一般是 bash)完全可以执行;其次,shell 命令执行时,node_modules/.bin 子目录下的所有可执行脚本命令会放入至 $PATH 全局环境变量中,等到 shell 命令执行完毕,$PATH 才会恢复原样.最后 node_modules/.bin 子目录下的所有可执行脚本命令实际上是与 node_modules 下可执行模块建立了软链接的,因此在 node 此类项目下,即使全局环境变量下不存在的脚本命令,依然可以通过 node_modules 中的可执行模块在当前项目下转化执行.
  
  线性图: node_modules/(可执行模块) -> 通过软链接 => node_modules/.bin/(可执行脚本命令) -> 通过 shell 脚本命令执行(添加至 $PATH 全局环境变量) => 即使全局环境变量下不存在的脚本命令也能执行.
  
# javascript

> 闭包

  什么是闭包?
  
  解: A 函数内包裹 B 函数,B 函数在非 A 函数作用域内实行调用,依然能够使用或者调用 A 函数内的变量或者函数,这就是闭包.
  
# webpack

> terser-webpack-plugin

  众所周知,在 webpack 4.x以及之后的版本,开启 mode: 'production',相当于默认添加了 terser-webpack-plugin,会自动为构建打包文件压缩混淆.那么有时会发现开启了 mode: 'production',却不能实现压缩混淆,这是为什么呢?
  
  解: 原因很有可能在 optimization.minimizer 里,有时会在里面配置一些对其他资源模块的压缩等功能的插件,而忘记 minimizer 属性值本身会对默认值进行覆盖,导致默认开启的 terser-webpack-plugin 不生效,那么在这时候需要对默认值进行合并,在 minimizer 的最后添加 '...' 元素就可生效.
  
  ```javascript
  module.exports = {
    //...
    optimization: {
        minimizer: [
            //...
            '...'
        ]
    }
    //...
  }
  ```

# ssr

> 作用

  ssr 服务端渲染相对于浏览器客户端渲染,其作用是什么?又有什么优势?
  
  - HTML 模板以及数据等资源都存放于服务端,内网拉取资源速度更快.
  - 相比于浏览器加载 HTML 以及接口数据等多个资源请求,ssr 服务端渲染只需要加载拼装好数据的一个 HTML 模板即可,大大减少了资源请求次数.
  - 对于用户来说,最直观的就是,大大减少了页面白屏的时间;而对于开发一方来讲,SEO 爬虫页面解析更友好.

# node

> __dirname

  __dirname 与 process.cwd() 的区别是什么?
  
  解: __dirname 指的是当前文件所在的位置目录. process.cwd() 指的是 node 所作用的位置目录.默认值为当前项目的根目录,可以实行修改: process.chdir(//...),修改后 process.cwd() 就会发生改变.
  
> require

  require.resolve 的作用是什么?
  
  解: 用于探查某个模块的完整路径
  
