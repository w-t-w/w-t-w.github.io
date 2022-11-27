---
title: think of css
date: 2022-11-21 20:01:20
tags: [css]
categories: css
---

# css

#### 水平居中

对于父子关系的布局，如何让子元素居中就是一个课题~

> text-align + inline-block

  ```css
  .parent {
    text-align: center;
}

.children {
    display: inline-block;
}
  ```

> absolute + transform(translate)

  ```css
  .parent {
    position: relative;
}

.children {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
}
  ```

> table + margin

  ```css
  .parent {
    display: table;
    margin: 0 auto;
}

.children {
}
  ```

> flex + justify-content

  ```css
  .parent {
    display: flex;
    justify-content: center;
}

.children {
}
  ```

#### 垂直居中

> table-cell + vertical-align

  ```css
  .parent {
    display: table-cell;
    vertical-align: middle;
    height: 100px;
}

.children {
}
  ```

> absolute + transform(translate)

  ```css
  .parent {
    position: relative;
    height: 100px;
}

.children {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
}
  ```

> flex + align-items

  ```css
  .parent {
    display: flex;
    align-items: center;
}

.children {
}
  ```

#### 水平垂直居中

> flex

  ```css
  .parent {
    display: flex;
    justify-content: center;
    align-items: center;
}

.children {
}
  ```

> absolute + transform(translate)

  ```css
  .parent {
    position: relative;
}

.children {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}
  ```

#### BFC

> 分类

- 浮动元素 float,除了 none 以外的情况
- 定位元素 position(absolute、fixed)
- display(inline-block、table-caption、table-cell)
- overflow(auto、hidden、scroll)

> 特性

- BFC 会形成一个独立的容器,容器内的元素不受容器外元素的影响
- BFC 的高度计算会联同浮动元素的高度
- BFC 的区域不会与浮动元素的区域重叠

#### flex

> 分类

- flex-grow: 设置元素的扩展比例
- flex-shrink: 设置元素的收缩比例
- flex-basis: 设置元素的初始值

#### 重绘和回流

> 区别

- 回流是由于页面 DOM 结构发生变化导致的,而重绘则是页面上存在一些样式变化就会发生重绘,不一定是 DOM 结构发生变化导致的
- 回流一定会导致重绘,而重绘不一定会导致回流

#### CSS 三角形

> 示例

```css
.box {
    width: 0;
    height: 0;
    border-bottom: 50px solid gray;
    border-top-color: transparent;
    border-left-color: transparent;
    border-right-color: transparent;
}
```

#### CSS 梯形

> 示例

```css
.box {
    width: 20px;
    height: 20px;
    border: 50px solid gray;
    border-top-color: transparent;
    border-left-color: transparent;
    border-right-color: transparent;
}
```
