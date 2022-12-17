---
title: JavaScript Garbage Collecation
date: 2022-12-17 19:31:44
update: 2022-12-17 19:31:44
tags:
  - JavaScript
categories:
  - JavaScript
description: 'Garbage Collecation'
keywords: 'Garbage Collecation,GC,JavaScript'
top_img: /assert/index-image.png
cover: false
---

## <center>JavaScript Garbage Collecation（垃圾回收）</center>

#### 1. 垃圾回收定义
  找出不再使用的变量，然后释放掉其占用的内存，但是这个过程不是时时的，因为其开销比较大，所以垃圾回收器会按照固定的时间间隔周期性的执行。
    
    
#### 2. JS垃圾回收机制
  - 标记清除
      
      JavaScript中最常用的垃圾回收方式。当变量进入执行环境标记为“进入环境”，离开环境将其标记为“离开环境”，等待清除
      
  - 引用计数
      
      语言引擎存在一张“引用表”，保存了内存里面所有的资源（通常是各种值）的引用次数，如果一个值的引用次数是0，就表示这个值不再被使用了，因此可以将这块内存释放
      
      引用计数的问题：循环引用
      
      ```javascript
      function func() {
        let obj1 = {}
        let obj2 = {}
      
        obj1.next = obj2
        obj2.next = obj1
      }
      ```
      
      当函数执行结束后，返回值为`undefined`，所以整个函数以及内部变量都应该被回收，但是根据引用计数方法，`obj1` 和 `obj2` 的引用次数都不为0，所以它们不会被回收，解决循环引用的办法：不使用它们的时候手动将它们设为`null`
        
#### 3. 内存泄漏
 - 意外的全局变量
    
    ```javascript
    function foo(arg) {
        bar = "this is a hidden global variable"; // 定义成了全局对象(window)属性
      this.variable = 0 // this指向了全局对象(window)
    }
    
    // 启用严格模式可以避免意外的全局变量
    ```
    
 - 被遗忘的计时器或回调函数
 - 闭包
 - 没有清理的DOM元素引用
    
#### 4. 避免内存泄漏的一些方式
  - 减少不必要的全局变量或者生命周期较长的对象，及时对无用的数据进行垃圾回收
  - 注意程序逻辑，避免“死循环”
  - 避免创建过多的对象，不使用的时候手动将它们设为null
    
#### 5. 垃圾回收使用场景优化
  - 数组优化
      
      将`空数组[]`赋值给数组对象是清空数组的捷径，但是`空数组`也会占用内存。实际上`array.length = 0`也能清空数组，并且同时能实现数组重用，减少内存垃圾产生
      
    
  - 对象尽量复用
      
      对象尽量复用，不用的对象尽可能设置为null，尽快被垃圾回收掉