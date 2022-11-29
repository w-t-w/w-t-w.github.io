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

#### request

> 请求头

  - content-type: 向服务器端请求获取资源的类型
  - referer: 包含了当前请求页面的来源页面的地址
  - user-agent: 则是用于告知服务器端,访问者是由什么工具请求的

#### OPTIONS

> 概念

  HTTP request OPTIONS 相当于是 Chrome 浏览器发的一个"预检"请求,它会向服务器申请权限检测信息,如果申请通过,浏览器才会真正向服务器发送一个正式的 HTTP 请求.
  
> 场景

  - 跨域请求

#### redirect

> 状态码

  301: A 地址资源被永久性的移除,A 地址资源不可被访问
  302: A 地址资源被暂时性的转移,A 地址资源仍然可被访问,只是临时的从 A 地址跳转到了 B 地址

#### HTTP 缓存策略

> 分类

  - 协商缓存
    - ETag 与 If-None-Match: 通过 URL 资源标识符与服务器端实行协商,如果服务器端发现 ETag 与 If-None-Match 不相同,则会返回数据,状态码 200;否则会返回状态码 304,浏览器取本地缓存
    - Last-Modified 与 If-Modified-Since: 通过最后修改时间戳与服务器端实行协商,如果服务器端发现 Last-Modified 与 If-Modified-Since 不相同,则会返回数据,状态码 200;否则会返回状态码 304,浏览器取本地缓存
  - 强制缓存
    - Expires: 服务器端返回响应头加入 Expires 时间戳,在这个时间戳之前,浏览器端只能取本地缓存
    - Cache-Control
      - max-age: 服务器端返回响应头加入 Cache-Control: max-age = 20000,表示当前资源在 20000 秒内都不会再请求,浏览器取本地缓存
      - immutable: 浏览器永远只能取本地缓存
      - no-cache: 使用缓存前,强制要求把请求提交给服务器端实行验证
      - no-store: 不存储有关客户端请求或服务端响应的任何内容,不使用任何缓存

> 优先级

  - 强制缓存与协商缓存
    - 会先判断强制缓存,如果强制缓存生效,直接使用缓存,否则发请求跟服务器协商,看要不要使用缓存
  - 协商缓存
    - ETag 与 Last-Modified
      - ETag > Last-Modified,原因是 Last-Modified 设计上只精确到秒,而 ETag 则是随时更新的,比 Last-Modified 更加精确
  - 强制缓存
    - Expires 与 Cache-Control
      - Cache-Control > Expires