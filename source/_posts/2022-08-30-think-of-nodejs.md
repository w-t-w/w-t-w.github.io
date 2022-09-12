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