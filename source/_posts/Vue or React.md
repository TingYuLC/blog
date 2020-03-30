---
title: Vue or React
categories: JavaScript
tags: [Vue, React]
date: "2019-01-09"
---

## 相同点

1. 使用 Virtual DOM
2. 提供了响应式 (Reactive) 和组件化 (Composable) 的视图组件。
3. 将注意力集中保持在核心库，而将其他功能如路由和全局状态管理交给相关的库。

<!--more-->

## 不同点

### 生命周期：

##### Vue有八个生命周期：

**beforeCreate： 实例初始化之后调用，数据观测（data observer）和 事件配置（event/watcher）之前调用。**

**在这个阶段，无法使用this变量，data，methods和watcher等局不能调用。**

**created：实例已完成数据观测（data observer）和事件配置（event/watcher），可以操作data，methods和watcher，但挂载还没开始，无法操作dom节点。**

**beforeMounte：相关的render方法已执行，但挂载还没开始。**

**mounted：挂载已完成，可以操作dom节点。**

**beforeUpdate：数据改变时调用，此时Virtual Dom还没更新。**

**updated：Virtual Dom已经更新完成。**

**beforeDestory：实例销毁前调用。**

**destoryed：实例销毁时调用。**

##### React的生命周期大体分为三部分：

**1.组件实例被创建被插入到Dom前：**

**constructor：**

**componentWillMount：**

**render：**

**componentDidMount：**

**2.state或者props改变导致更新时调用：**

**componentWillReceiveProps：**

**shouldComponentUpdate：**

**componentWillUpdate：**

**render：**

**componentDidUpdate：**

**3.组件从Dom销毁时调用：**

**componentWillUnmount：**