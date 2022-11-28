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
- Virtual Dom 与 Diff 算法配合,可保证性能

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