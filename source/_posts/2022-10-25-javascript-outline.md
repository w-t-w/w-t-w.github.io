---
title: javascript outline
date: 2022-10-25 11:41:26
tags: [javascript, es6, outline]
category: javascript
---

# ES6

### var 

- 变量提升
- 重复声明
- 全局作用域绑定

### let、const

- 不再拥有 var 的缺陷特性
- TDZ(临时死区)
- 块级作用域绑定

### let

- 变量声明

### const

- 常量声明
- 优先声明最佳实践

### 字符串

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

### 函数

  - 参数的默认值,注意 TDZ 临时死区
  - 不定参数,注意参数集合必须在参数末尾
  - 展开运算符
  - 功能增强的 Function 构造声明方式
  - name 捕获函数的名称以便于堆栈追踪
  - 元属性: new.target, 绑定内置对象 \[\[Construct\]\],当函数执行触发内置对象 \[\[Call\]\] 时,返回 undefined
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
  


  - 原理: 避免建立多堆栈存储,防止堆栈溢出、内存泄露,单堆栈循环使用提高效率
  - 应用: 递归
  - 还没有被大规模推行,目前只在 safari 浏览器中实现,其他浏览器开发环境应用极其容易发生堆栈溢出、内存泄露

### 对象

  - 对象属性方法简化
  - 可计算的对象属性方法
  - 方法
    - 判断两个值是否相等: Object.is,注意除了 (+0,-0)、NaN 之外,其他执行结果与 === 相同
    - 浅复制: Object.assign
  - 重复对象属性方法
  - 自有属性方法枚举排序: Object.getOwnPropertyNames、Reflect.ownKeys、for-in、JSON.stringify、Object.keys 以及 Object.entries 在各大厂商都是按照数字提升升序排列,字符串按照添加顺序排列.
  - 增强原型: Object.getPrototypeOf、Object.setPrototypeOf
  - 原型访问 Super 引用: 在正式的方法也就是简化的对象方法内,super 引用可直接绑定 \[\[HomeObject\]\] 访问原型.

### 解构赋值

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

### Symbol

   - 基本用法,注意绑定于内置对象 \[\[Description\]\] 内
   - 共享体系: Symbol.for、Symbol.keyFor
   - 属性遍历: Object.getOwnPropertySymbols、Reflect.ownKeys
   - wellKnown-Symbols
     - Symbol.hasInstance: 作为方法,用于重新定义 instanceof,判断构造对象是否在原型链上
     - Symbol.isConcatSpreadable: 作为属性,用于重新定义类数组对象,判断其是否可被合并
     - Symbol.search、Symbol.replace、Symbol.match、Symbol.split: 作为方法,用于重新定义字符串查找、替换、匹配以及分割方法
     - Symbol.toPrimitive: 作为方法,用于重新定义对象强制类型转换后的结果(包含数字模式以及字符串模式转换)
     - Symbol.toStringTag: 作为属性,用于重新定义对象类型识别
     
### Set

   - 基本用法
   - 属性
     - size: 获取集合的长度
   - 方法
     - add: 向集合内添加数据
     - has: 判断集合内是否存在指定数据
     - delete: 删除集合内指定数据
     - clear: 清空集合内所有数据
     - forEach: 循环集合内数据、数据索引实行操作
     - keys: 循环集合内数据索引实行操作
     - values: 循环集合内数据实行操作
     - entries: 循环集合内数据索引、数据实行操作,类似于 forEach
   - 初始化声明并添加数据: new Set([])
   - 转化为数组: \[...set\],注意 set 构造对象必须配合数组、展开运算符才可实行转化

### WeakSet

   - 基本用法
   - 区别
     - 只能向弱类型集合中添加引用类型数据
     - size、clear、forEach、keys、values 以及 entries 失效
     - 一旦与添加的引用类型数据失去绑定或者引用类型数据失效,集合内数据就会被释放并被垃圾回收机制及时清理,防止堆栈溢出、内存泄露
   
### Map

   - 基本用法
   - 属性
     - size: 获取字典的大小
   - 方法
     - set: 向字典中添加映射、数据
     - get: 根据指定映射获取字典内相对应的数据
     - has: 根据指定映射判断字典内是否存在相对应的数据
     - delete: 根据指定映射删除字典内相对应的数据
     - clear: 清空字典内所有的映射、数据
     - forEach: 循环字典内数据、映射实行操作
     - keys: 循环字典内映射实行操作
     - values: 循环字典内数据实行操作
     - entries: 循环字典内映射、数据实行操作,类似于 forEach
   - 初始化声明并添加映射、数据: new Map([[]])

### WeakMap

   - 基本用法
   - 区别
     - 只能向弱类型字典中添加引用类型的映射以及所对应的数据
     - size、clear、forEach、keys、values 以及 entries 失效
     - 一旦与添加的引用类型映射失去绑定或者引用类型映射失效,字典内映射以及所对应的数据就会被释放并被垃圾回收机制及时清理,防止堆栈溢出、内存泄露
     - 私有数据
       - ES5 使用私有对象保存,闭包产生的内存数据永远无法被清除,久而久之会出现堆栈溢出、内存泄露
       - ES6 使用 WeakMap 保存更加简洁,不会产生任何堆栈溢出、内存泄露的问题

### Iterator

   - 基本用法,注意可以模拟迭代器
   - 访问默认迭代器: 可迭代的对象(数组、集合、字典以及字符串等)调用 \[Symbol.iterator\]() 即可
   - 创造可迭代对象: 实际上就是对于普通对象模拟迭代器,{*\[Symbol.iterator\]() {yield *Object.values(this);}},注意这里还使用了委托生成器
   - 可迭代对象
     - 数组: 默认迭代器是针对数组元素,实际上调用的是其 values 方法
     - 集合: 默认迭代器是针对集合数据,实际上调用的是其 values 方法
     - 字典: 默认迭代器是针对字典映射以及其所对应的数据,实际上调用的是其 entries 方法
     - 字符串: 默认迭代器是针对字符串中的字符,注意这里已经扩展至 UTF-16 范围内的字符
     - NodeList 对象: 默认迭代器是针对 NodeList 集合中的节点元素

### Generator

   - 基本用法
   - 生成器函数表达式
   - 生成器对象方法
   - 向迭代器传递参数
   - 在迭代器中抛出错误,注意抛出错误后,在没有 try-catch 的情况下,会直接停止,不会继续迭代
   - 在生成器中返回语句
   - 委托生成器,注意对于数组、函数的委托生成,会对其中的可迭代元素分别迭代
   - 异步生成器,注意异步执行时的事件循环执行机制

### class

   - 基本用法
   - 特性
     - 存在 TDZ 临时死区
     - 类赋值不可在类内部实行修改
     - 只可使用 new 构造调用
     - 类原型链上的方法不可枚举且不可使用 new 构造调用
     - 类原型链上的访问器属性不可枚举
     - 相对于 ES5 继承时,继承是基于派生类的 this,父类只是在此基础上实行修饰,ES6 的继承则是永远基于父类的 this,派生类是在此基础上实行修改,两套继承模式完全相反
   - 类表达式
   - 命名的类表达式
   - 类作为参数传递
   - 类声明的同时实行 new 构造调用
   - 类的访问器属性
   - 可计算的类成员
   - 生成器方法: 注意可创作可迭代构造对象
   - 类静态成员
   - 继承与派生
     - 派生类的方法遮蔽
     - 派生类所继承的访问器属性
     - 派生类所继承的静态成员
     - 继承自普通函数或者函数表达式
     - 内置对象继承
     - Symbol.species: 作为静态的访问器属性,用于重新自定义创建派生类对象,确定值类型
   - 元属性 new.target 在类中的使用
     - 抽象基类

### 改进的数组

   - Array.of: 用于规整数组声明时混乱意义的参数,注意 Array.of 创建的类对象并不是由静态访问器属性 \[Symbol.species\] 来确定值类型,而是直接绑定至构造对象 this 上,由构造函数来确定值类型
   - Array.from: 用于将可迭代对象以及类数组对象转化为数组,同时可实行操作,并可改变操作函数的 this 绑定,注意 Array.from 创建的类对象并不是由静态访问器属性 \[Symbol.species\] 来确定值类型,而是直接绑定至构造对象 this 上,由构造函数来确定值类型
   - 方法
     - find: 匹配符合条件的第一个数组元素
     - findIndex: 匹配符合条件的第一个数组元素下标
     - fill: 向指定的数组位置填充覆盖指定的数据
     - copyWithin: 向指定的数组位置填充覆盖指定数组范围的数据
   - 定型数组
     - 创建方式
       - 使用自定义数组缓冲区创建视图
       - 使用定型数组声明创建
       - 使用已声明的定型数组构造对象创建
         - 注意,上述创建方式都可指定字节偏移量、以及字节长度
     - 区别
       - 相似之处
          - 通用方法
            - map
            - forEach
            - keys
            - values
            - entries
            - some
            - every
            - filter
            - reduce
            - reduceRight
            - indexOf
            - lastIndexOf
            - join
            - reverse
            - sort
            - find
            - findIndex
            - fill
            - copyWithin
          - 同属于可迭代对象,可使用展开运算符
       - 不同之处
         - 定型数组缺失方法
           - push
           - pop
           - shift
           - unshift
           - concat
           - splice
         - 定型数组缺失特性
           - 数组元素不可扩展
           - 数组长度不可配置、不可修改
         - 定型数组特有方法
           - set: 用于向定型数组中覆盖指定范围的数组元素
           - subarray: 类似于 slice 方法,用于截取定型数组

### promise

   - 基本用法: 注意 promise 是绑定至内置对象 \[\[PromiseState\]\] 内
     - pending: 处理中
     - fulfilled: 处理完成
     - reject: 拒绝处理
   - 创建未完成的 Promise: new Promise((resolve, reject) => {})
   - 创建已处理的 Promise: Promise.resolve,Promise.reject,注意对于 Promise.resolve 如果传入 Promise 参数,则会将此 Promise 直接返回
   - 全局 Promise 拒绝处理程序: 在事件循环执行机制中,若 Promise 被拒绝,且没有拒绝处理程序时,会触发执行 unhandledRejection 事件; 若 Promise 被拒绝,且监听到拒绝处理程序时,则会触发 rejectionHandled 事件,注意若同步立即设置拒绝处理程序,则不会触发任何事件
     - unhandledRejection: 在 Node.js 中使用 process.on 监听,在 web 中则是触发 window 事件 onunhandledrejection
     - rejectionHandled: 在 Node.js 中使用 process.on 监听,在 web 中则是触发 window 事件 onrejectionhandled
   - Promise 执行抛出错误: 使用 catch 拦截
   - Promise 串行链
     - 在 Promise 链中抛出错误
     - 在 Promise 链中的返回值
     - 在 Promise 链中返回 Promise
   - Promise.all: 当 Promise 数组中的每一个处理都完成时,才会实行返回,接收到的数据也才是一个数组,分别代表着每一个完成处理传递的数据,一旦存在有一个拒绝处理,则会立即返回,接收的将是拒绝处理程序传递的数据
   - Promise.race: 当 Promise 数组中任意一个处理完成或者拒绝时,都会立即返回,接收到完成或者拒绝处理程序传递的数据
   - 派生类继承自 Promise: 注意 Promise 的静态成员 resolve、reject 创建的类对象是由静态访问器属性 \[Symbol.species\] 确定值类型的,由此,类对象是在派生类的原型链上
   - Promise 的异步生成器

### proxy

   - 基本用法
   - set 代理陷阱验证对象属性
   - get 代理陷阱验证对象结构
   - has 代理陷阱隐藏对象属性
   - deleteProperty 代理陷阱防止删除对象属性
   - 原型代理陷阱: setPrototypeOf、getPrototypeOf
     - 区别: 除了主要功能相同之外
       - Object.setPrototypeOf: 是绑定至内置对象 \[\[SetPrototypeOf\]\] 内,其执行结果如果成功则返回第一个参数,失败则抛出错误
       - Reflect.setPrototypeOf: 是绑定至内置对象 \[\[SetPrototypeOf\]\] 的包裹器,存在数据验证,只会返回 true 或者 false
       - Object.getPrototypeOf: 是绑定至内置对象 \[\[GetPrototypeOf\]\] 内,可将基本类型的数据强制转化为相关的构造对象
       - Reflect.getPrototypeOf: 是绑定至内置对象 \[\[GetPrototypeOf\]\] 的包裹器,存在数据验证,参数只能接收引用类型对象,传递基本类型数据会直接抛出错误
   - 对象可扩展性代理陷阱: isExtensiable、preventExtensions
     - 区别: 除了主要功能相同之外
       - Object.isExtensiable: 可将基本类型的数据强制转化为相关的构造对象
       - Reflect.isExtensiable: 参数只能接收引用类型数据,传递基本类型数据会直接抛出错误
       - Object.preventExtensions: 可将基本类型的数据强制转化为相关的构造对象
       - Reflect.preventExtensions: 参数只能接收引用类型数据,传递基本类型数据会直接抛出错误
   - 对象属性描述符代理陷阱: defineProperty、getOwnPropertyDescriptor
     - 使用 defineProperty 来添加属性限制
     - 描述符属性的限制: defineProperty、getOwnPropertyDescriptor 所能操作的描述符属性只有 value、get、set、writable、enumerable、configurable
     - 区别: 除了主要功能相同之外
       - Object.defineProperty: 执行结果如果成功则返回第一个参数,失败则抛出错误
       - Reflect.defineProperty: 只会返回 true 或者 false
       - Object.getOwnPropertyDescriptor: 可将基本类型的数据强制转化为相关的构造对象
       - Reflect.getOwnPropertyDescriptor: 参数只能接收引用类型数据,传递基本类型数据会直接抛出错误
   - ownKeys 代理陷阱
   - 函数执行 apply、construct 代理陷阱
     - 验证函数参数
     - 不使用 new 构造调用: 通过在 apply 代理陷阱中调用 construct 代理陷阱即可绕过函数的元属性 new.target new 构造调用校验
     - 覆写抽象基类: 通过在 construct 代理陷阱中将 new.target 修改成普通类或者普通函数即可绕过 new.target 抽象基类校验
     - 可调用的类: 通过在 apply 代理陷阱中调用 construct 代理陷阱或者直接在 apply 代理陷阱中使用 new 构造调用即可绕过类的只可实行 new 构造调用的校验
   - 可撤销的代理陷阱: 通过 Proxy.revocable revoke 方法可实现撤销代理陷阱
   - 拥有数组特性的类
   - 将代理陷阱作为原型
     - 在原型中使用 set 代理陷阱
     - 在原型中使用 get 代理陷阱
     - 在原型中使用 has 代理陷阱
     - 将代理陷阱作为类的原型

### 模块的导入导出

   - 普通用法
     - 导出模块
     - 导入模块
       - 导入单个绑定
       - 导入多个绑定
       - 导入整个模块
     - 重命名导入导出模块
     - 模块的默认值
       - 导入模块的默认值
       - 导出模块的默认值: 注意只能导出一个模块的默认值,出现导出多个默认值会直接抛出语法错误
       - 重命名导入导出模块默认值
     - 重新导出绑定
     - 无绑定导入模块
   - ESM 的特性
     - 静态分析
     - 赋值引用
   - Web 浏览器内同步加载模块的顺序
     - 下载并解析入口模块
     - 递归下载并解析入口模块所导入的资源
     - 递归执行入口模块所导入的资源
     - 执行入口模块

# ES6+

### 数字

   - 方法
     - Number.isInteger: 判断数据是否是一个整数
     - Number.isSafeInteger: 判断数据是否是一个安全整数,注意安全整数的范围在 -(2^53 - 1) 与 2^53 - 1 之间
     - Number.MAX_SAFE_INTEGER: 安全整数范围内的最大值
     - Number.MIN_SAFE_INTEGER: 安全整数范围内的最小值
   - 运算符
     - **: 幂运算符,与 Math.pow() 的执行结果相同

### 数组

   - includes: 用于判断数组内是否含有某个数据