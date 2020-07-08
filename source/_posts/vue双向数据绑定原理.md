---
title: vue双向数据绑定原理
date: 2020-03-16 22:58:20
tags: 
    - vue
categories: 
    - 前端
---

核心是利用ES5的Object.defineProperty,这个方法不兼容ie8及以下浏览器，这也是vue为什么不能兼容ie8及以下浏览器的原因。

* Object.defineProperty方法可以直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。
* observe的功能就是用来监测数据的变化。Observe是一个类，它的作用是给对象属性添加getter和setter，用于依赖收集和派发更新。实现方式是给非VNode的对象类型数据添加一个Observe，如果已经添加的则直接返回，否则在满足一定条件下去实例化一个Observe对象实例。

## 依赖收集 getter（重点关注以下两点）
* const dep = new Dep()   //实例化一个Dep实例
* 在get函数中通过dep.depend做依赖收集

Dep是一个class，它定义了一些属性和方法，它有一个静态属性target，这是一个全局唯一的watcher（同一时间内只能有一个全局的watcher被计算）。Dep实际上就是对watcher的一种管理，Dep脱离watcher单独存在是没有意义的。watcher和dep就是典型的观察者设计模式。

watcher是一个class，在它的构造函数中定义了一些和Dep相关的属性：
```
this.deps = []
this.newDeps = []
this.depIds = new Set()
this.newDepIds = new Set()
```
### 收集过程
当我们实例化一个渲染watcher的时候，首先进入watcher的构造函数逻辑，然后执行它的this.get()方法，进入get函数把Dep.target赋值为当前渲染watcher并压线（为了恢复用）。接着执行vm._render()方法，生成渲染函数VNode，并且在这个过程对vm上的数据访问，这个时候就触发数据对象的getter（在此期间执行Dep.target.addDep(this）方法，将watcher订阅到这个数据持有的dep的subs中，为后续数据变化时通知到哪些subs做准备）。然后递归遍历添加所有子项getter。
watcher在构造函数中初始化两个Dep实例数组。newDeps代表新添加的Dep实例数组。newDeps代表新添加的Dep实例