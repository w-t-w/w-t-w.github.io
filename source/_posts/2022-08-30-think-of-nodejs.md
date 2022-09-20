---
title: think of nodejs
date: 2022-08-30 18:43:30
tags: [npm, nodejs]
categories: nodejs
---

# npm

#### browserslist

> 作用

  用于编译转化样式文件以兼容浏览器的对于各类浏览器版本的筛选.
  
> 分类

  \[">0.2%"\]: 指的是现阶段的用户比例>0.2%的所有浏览器版本.
  \["last 2 versions"\]: 指的是现阶段所有浏览器最近发布的两个版本.
  \["not dead"\]: 指的是现阶段没有被删除销毁的所有浏览器版本.
  \["defaults"\]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
  \[">0.2%","last 2 versions","not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
  \[">0.2% and last 2 versions and not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的交集.
  \[">0.2% or last 2 versions or not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
  \["not >0.2%"\]: 指的是现阶段的用户比例 <=0.2% 的所有浏览器版本.
  
#### version

> 作用
  
  用于发布构建/依赖/工具时,对于版本号的分类管理.

> 分类
  
  - patch: 是对此构建/依赖/工具的补丁,常指修复一些Bug、代码样式美化以及变更文档等.
  - minor: 是对此构建/依赖/工具在当前 API 下新增一个功能.
  - major: 是对此构建/依赖/工具与当前 API 出现比较大的不兼容,也就是 BREAKING CHANGES 的重构.

#### pre-version

> 作用

  先行版本号是作为构建/依赖/工具稳定版本生命周期的线性图,可确保其上线的质量、安全以及稳定性.

> 分类

  - alpha: 是内测版本,一般会有很多 Bug,内测人员使用.
  - beta: 测试版本,会开放一部分外部人员测试,这阶段还是会继续开发新功能.
  - rc: 公测版本,会完全对外开放测试,不再开发添加新功能.
 
#### npx

> 作用

  是 npm 自带的包执行器.其主要作用就是可随时执行项目内安装的可执行模块,且可在全局环境变量下脚本命令不存在时,自动安装执行.

> 原理

  其原理非常简单,就是轮询 node_modules/.bin 子目录以及 $PATH 全局环境变量下是否存在指定的脚本命令,如果存在就执行,本地不存在则自动安装下载执行.
  
> 参数

  - --no-install: 指的是强制使用本地模块,禁止下载远程模块.