---
title: think of browser
date: 2022-11-24 08:56:13
tags: [browser]
categories: browser
---
# browser

#### process

> 分析

  - 浏览器的进程
    - 浏览器进程
    - 渲染进程
      - GUI 线程: 用于处理解析渲染 html、css
      - JS 线程: 也就是程序员所说的所谓 "JS 单线程"
      - 定时器线程: 处理定时器(setTimeout、setInterval),将符合条件的回调事件移入至事件触发线程
      - 事件触发线程: Event Loop 事件循环执行机制
      - 异步 HTTP 请求线程: 处理 http 请求,将符合条件的回调事件移入至事件触发线程
    - GPU 进程
    - 插件进程
    - 网络进程