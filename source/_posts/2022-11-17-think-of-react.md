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
    - componentDidUpdate: 在组件渲染更新后要执行的一些操作
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

  key 适用于那些需要频繁变换位置的组件节点,稳定的类型指向会保证这些组件节点不会被剃掉删除,增加 DOM 操作开销,而只是替换位置