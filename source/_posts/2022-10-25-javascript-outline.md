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
  - 判断是否存在粘滞描述符: sticky
  - 获取描述符内容: flags()
  - 获取正则内容: source()
  - 复制描述符覆盖

> 模板字符串

  - 基本语法(占位符)
  - 多行字符串

> 模板标签

  - 字符串集合长度 - 变量集合长度 = 1
  - 字符串集合中每一个元素都拥有获取其原生内容的方法: raw

# 函数

  - 参数的默认值,注意 TDZ 临时死区
  - 不定参数,注意参数集合必须在参数末尾
  - 展开运算符
  - 功能增强的 Function 构造声明方式
  - name 捕获函数的名称以便于堆栈追踪
  - 元属性: new.target, 绑定内置对象 \[\[Constructor\]\],当函数执行触发内置对象 \[\[Call\]\] 时,返回 undefined
  - 块级函数声明

> 箭头函数

  - 特性

    - 无 this、super、arguments 以及 new.target
    - 无原型链
    - 不能使用 new 构造调用
    


  - 基本语法
  - this、arguments 会继承自最近词法作用域执行时的上下文对象

> 尾调用优化

  - 特性

    - 必须为函数的最后一步执行
    - 执行的结果必须实行返回
    - 执行时不得运算或者操作函数词法作用域内变量,防止产生闭包
  


  - 原理: 避免建立多堆栈存储,防止堆栈溢出,单堆栈循环使用提高效率
  - 应用: 递归
  - 还没有被大规模推行,目前只在 safari 浏览器中实现,其他浏览器开发环境应用极其容易发生堆栈溢出

# 对象

  - 对象属性方法简化
  - 可计算的对象属性方法
  - 方法
    - 判断两个值是否相等: Object.is,注意除了 (+0,-0)、NaN 之外,其他执行结果与 === 相同
    - 浅复制: Object.assign
  - 重复对象属性方法
  - 自有属性方法枚举排序: Object.getOwnPropertyNames、Reflect.ownKeys、for-in、JSON.stringify 以及 Object.keys 在各大厂商都是按照数字提升升序排列,字符串按照添加顺序排列.
  - 增强原型: Object.getPrototypeOf、Object.setPrototypeOf
  - 原型访问 Super 引用: 在正式的方法也就是简化的对象方法内,super 引用可直接绑定 \[\[HomeObject\]\] 访问原型.

# 解构赋值

  - 对象解构
    - 解构赋值
    - 默认值
    - 非同名变量赋值
    - 嵌套解构
  - 数组解构
    - 解构赋值
    - 默认值
    - 嵌套解构
    - 不定元素,注意跟不定参数一致,元素集合必须在数组末尾
  - 混合解构
  - 解构参数
    - 必须传值
    - 默认值