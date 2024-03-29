---
title: "BFC"
date: 2022-07-31T14:53:23+08:00
draft: false
tags: [""]
categories: [""]
typora-root-url: ..\..\static
---

## 一句话总结

BFC是一个隔离的独立容器，内部的元素与外界的元素互不干扰。

## 前置知识

### 常见定位方案

- 普通流 (normal flow)

> 在普通流中，元素按照其在 HTML 中的先后位置至上而下布局，在这个过程中，行内元素水平排列，直到当行被占满然后换行，块级元素则会被渲染为完整的一个新行，除非另外指定，否则所有元素默认都是普通流定位，也可以说，普通流中元素的位置由该元素在 HTML 文档中的位置决定。

- 浮动 (float)

> 在浮动布局中，元素首先按照普通流的位置出现，然后根据浮动的方向尽可能的向左边或右边偏移。

- 绝对定位 (absolute positioning)

> 在绝对定位布局中，元素会整体脱离普通流，因此绝对定位元素不会对其兄弟元素造成影响，而元素具体的位置由绝对定位的坐标决定。

## 概念

BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。 　　　

## 形成条件

- float为 `left|right`
- overflow为 `hidden|auto|scroll`
- display为 `table-cell|table-caption|inline-block|inline-flex|flex`
- position为 `absolute|fixed`
- 根元素

## BFC布局规则

- 内部的Box会在垂直方向，一个接一个地放置(即块级元素独占一行)。
- BFC的区域不会与float box重叠(**利用这点可以实现自适应两栏布局**)。
- 内部的Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠(**margin重叠三个条件:同属于一个BFC;相邻;块级元素**)。
- 计算BFC的高度时，浮动元素也参与计算。（清除浮动 ）
- BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。


## 使用场景

- 解决边距重叠问题
- 清除浮动（父级元素会计算浮动元素的高度）
- 防止margin重叠

## 应用场景

- 清除浮动：BFC 内部的浮动元素会参与高度计算，因此可用于清除浮动，防止高度塌陷
- 避免某元素被浮动元素覆盖：BFC 的区域不会与浮动元素的区域重叠
- 阻止外边距重叠：属于同一个 BFC 的两个相邻 Box 的 margin 会发生折叠，不同 BFC 不会发生折叠





> 参考资料
>
> [史上最全面、最透彻的BFC原理剖析](https://github.com/zuopf769/notebook/blob/master/fe/BFC%E5%8E%9F%E7%90%86%E5%89%96%E6%9E%90/README.md)
>
> [学习 BFC](https://juejin.cn/post/6844903495108132877)



