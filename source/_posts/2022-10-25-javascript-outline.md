---
title: javascript outline
date: 2022-10-25 11:41:26
tags: [javascript, es6, outline]
category: javascript
---
# var 

- 变量提升
- 重复声明
- 全局作用域绑定

# let、const

- 不再拥有 var 的缺陷特性
- TDZ(临时死区)
- 块级作用域绑定

# let

- 变量声明

# const

- 常量声明
- 优先声明最佳实践

# 字符串

> utf-8

  - 2^8 码位
  - 基本多文种平面
  - 字符位: charAt
  - 字符编码: charCodeAt
  - 编码转换: fromCharCode

> utf-16

  - 2^16 码位
  - 辅助平面
  - 字符串长度
  - 正则表达式匹配: u
  - 字符编码: codePointAt
  - 编码转换: fromCodePoint

> 方法

  - 获取字符子串: startsWith、endsWith、includes
  - 重复添加: repeat

> 正则表达式

  - 粘滞描述符: y
  - 获取描述符内容: flags()
  - 获取正则内容: source()
  - 复制描述符覆盖

> 模板字符串

  - 基本语法(占位符)
  - 多行字符串

> 模板标签

  - 字符串集合长度 - 变量集合长度 = 1
  - 字符串集合中每一个元素都拥有获取其原生内容的方法: raw
