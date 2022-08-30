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