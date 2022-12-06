---
title: think of react
date: 2022-11-17 11:48:59
tags: [react,source]
categories: react
---

# react

#### 特性

- 数据驱动
- JSX 语法糖动态声明更加灵活
- Virtual DOM 与 Diff 算法配合使用,可保证性能
- Fiber 的任务调度和数据结构令 react 的性能更出色

#### 基于 class

- 生命周期
  - 创建阶段
    - constructor: 初始化
    - getDerivedStateFromProps: 合并组件外的 props 至组件内 state,不推荐使用
    - render: react 组件内唯一的描述 UI 的方法,必须设置
    - componentDidMount: 在组件首次挂载结束之后,需要实行的如异步 http 资源请求的步骤
  - 更新阶段
    - getDerivedStateFromProps: 合并组件外的 props 至组件内 state,不推荐使用
    - shouldComponentUpdate: 判断组件是否可以实行渲染更新
    - getSnapShotBeforeUpdate: 在组件渲染更新前需要的统计计算
    - render: react 组件内唯一的描述 UI 的方法,必须设置
    - componentDidUpdate: 在组件渲染更新完成后要执行的一些操作
  - 卸载阶段
    - componentWillUnmount: 在组件卸载时,用于资源释放

#### Diff 算法

> 复杂度

  O(n ^ 3) -> O(n),只对比同层兄弟组件,不实行跨层对比

> 节点

  - 属性变化
  - 位置变化
  - 类型变化
  - 跨层移动

  上述节点的变化,react 处理的都很简单粗暴,不会实现跨层检索校验,只是进行简单的增加、删除以及替换,而这种情况是建立在 Virtual DOM 结构一般比较稳定,基本上不会出现跨层移动的情况,大多是同层的属性、位置以及类型的变化
  
> key

  key 适用于那些需要频繁变换位置的组件节点,稳定的类型指向会保证这些组件节点不会被剃掉删除,更不会额外增加 DOM 操作开销,而只是替换位置
  
#### HOC

> 特性
  
  - 属性代理
  - 反向集成
  - 渲染劫持

#### class 与 hooks

> 区别

  - class
    - 很少使用 class 类的特性: new 构造调用、继承与派生
    - class 的 react 组件复用过于复杂,必须基于 HOC,产生了冗余的逻辑,创建了不必要的组件节点
    - class 将业务逻辑全部分散在各个生命周期中,更关注于技术部分的逻辑
  - hooks
    - react 组件复用更加简洁,不会产生额外的逻辑,更不会创建多余的组件节点
    - 更关注于业务与技术的彻底分离

#### hooks

> 分类

  - useState
    - 用于维持组件的状态
    - 一旦组件拥有了状态,那么每一次使用时都会存在恢复状态的过程
  - useEffect
    - 用于利用副作用
    - 包含了基于 class 组件的生命周期: componentDidMount、componentDidUpdate、componentWillUnmount
    - 基于依赖项数组内的数组元素发生改变,回调函数才会执行
      - 依赖项数组内的数组元素必须在回调函数内使用,才会具有意义
      - 依赖项数组必须是一个常量数组,不能为变量
      - 依赖项数组内的数组元素不能为引用类型的数据
  - useCallback
    - 用于缓存回调函数
    - 为了避免不必要的创建回调函数和 react 组件的渲染更新
  - useMemo
    - 用于缓存计算结果
    - 为了避免不必要的计算过程和 react 组件的渲染更新
    - 可模拟 useCallback
  - useRef
    - 用于在多次渲染之间存储、共享数据
    - 大多应用于存储 setTimeout、setInterval 产生的 id,再就是应用于存储 DOM 对象
  - useContext
    - 用于实现数据绑定,在每一次祖先组件的数据发生更新时,子孙用到此数据的组件都会自动渲染更新

> 限制

  - hooks 只可在函数组件或者其他 hooks 内使用
  - hooks 只可在函数组件的顶级作用域内使用,不可在循环、条件判断或者嵌套函数内使用

> 混合

  要想在 react 类组件中使用 hooks,通过 HOC 的属性代理即可
  
#### Fiber

> 概念

  由于 react 处理组件的渲染更新是同步的,那么当同一时间内有大量的 react 组件需要渲染更新时,JS 线程处理的时间可能会很长,在这期间浏览器不会响应任何事件,因为 JS 线程和 GUI 线程是互斥的,那么 Fiber 就是用于解决此类问题的,Fiber 会将长时间的同步任务,切割成多个小的同步任务,启用新的任务调度和数据结构
  
> 特性

  - 新的任务调度
    - 当存在有高优先级的任务时,会先将浏览器让出来响应,等浏览器空闲了,再继续执行
  - 新的数据结构
    - 采用链表结构,可随时中断,也可接回重连
      - child: 父组件指向的第一个子组件的指针
      - return: 所有的子组件都存在指向父组件的指针
      - sibling: 在第一个组件之后,指向兄弟组件的指针