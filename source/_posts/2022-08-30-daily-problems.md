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

> script

  defer 与 async 的区别?

  - defer 不会阻止当前页面文档的渲染,当页面处理、解析、渲染完成之后,defer 脚本会先加载,然后按照顺序执行,加载执行完毕之后,触发 DOMContentLoaded 事件
  - async 不会阻止当前页面文档的渲染,脚本会在加载完成之后立即执行,DOMContentLoaded 事件可能发生在 async 脚本执行之前或者之后,不计入 DOMContentLoaded 事件统计范围内
  
# webpack

> terser-webpack-plugin

  众所周知,在 webpack 4.x以及之后的版本,开启 mode: 'production',相当于默认添加了 terser-webpack-plugin,会自动为构建打包文件压缩混淆.那么有时会发现开启了 mode: 'production',却不能实现压缩混淆,这是为什么呢?
  
  解: 原因很有可能在 optimization.minimizer 里,有时会在里面配置一些对其他资源模块的压缩等功能的插件,而忘记 minimizer 属性值本身会对默认值进行覆盖,导致默认开启的 terser-webpack-plugin 不生效,那么在这时候需要对默认值进行合并,在 minimizer 属性值数组的最后添加 '...' 元素就可生效.
  
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

> nvm

  在 node 版本为 v18,使用 nvm 时,为何全局下载 @angular/cli,使用 angular 会报以下错误?

  ![](https://image.white-than-wood.zone/daily_problems/nvm_node%3Afs.png)

  解: 由于在 node v14.18.0 极其之后的版本当中,fs 文件系统通过 node: 协议的 URL 的方法来实行访问 Node.js 的内置模块,例如: node:fs,而本地 nvm 默认使用的 node 版本为 v12.22.11,无法通过上述 node: 协议的 URL 的方法来实行访问 Node.js 的内置模块,由此会出现此类错误
  
# browser

> console

  "Warning: Mixed Content: The page at '\<URL\>' was loaded over HTTPS, but requested an insecure element '\<URL\>'. This request was automatically upgraded to HTTPS, For more information see \<URL\>"所表示的含义?如何消除?
  
  解: 表示当前浏览器 web https 安全协议页面中存在 http 请求的资源,也就是此浏览器 web 页面是由 http 和 https 协议混合内容的,由此会出现这种警告. 消除的话,就比较简单了,web 前端开发人员要确保页面上的每个资源都通过 HTTPS 加载.
  
# react

> hooks

  为何 hooks 不可写在循环、条件判断以及嵌套函数中呢?
  
  解: 由于 react hooks 使用的是新的数据结构: 采用链表的方式,可随时中断,也可接回重连.如果将 hooks 放入循环、条件判断以及嵌套函数中,就极有可能会导致 hooks 的取值出现偏移,从而导致异常发生
  
