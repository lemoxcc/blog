---
title: Symbol
date: 2021-02-16 15:23:41
update: 2021-6-24 15:47:34
tags:
  - JavaScript
  - ES6
categories:
  - JavaScript
description: 'JavaScript Symbol'
keywords: 'JavaScript,Symbol,ES6,基本类型'
top_img: /assert/index-image.png
cover: false
---

## <center>Symbol</center>

#### 1. 概述
  
  ES6引入了一种新的原始数据类型`symbol`，表示独一无二的值。它属于JavaScript语言的数据类型之一
  
  八大数据类型：`Symbol、undefined、null、Boolean、String、Number、Bigint、Object`
  
  ```JavaScript
  // 注意不要使用new命令，由于Symbol值不是对象，所以不能添加属性
  let s = Symbol('feature')
  
  typeof s // "symbol"
  
  // Symbol值不能与其他类型的值进行预算，会报错
  
  `your symbol is ${s}` // TypeError: can't convert symbol to string
  
  // 但是Symbol值可以显示转为字符串
  String(s) // 'Symbol(feature)'
  s.toString() // 'Symbol(feature)'
  
  // 还可以转为布尔值
  Boolean(s) // true
  ```
  
#### 2. 作为属性名的`Symbol`
  
  ```JavaScript
  let symbol = Symbol()
  
  // 第一种写法
  let obj = {}
  obj[symbol] = 'hello!'
  
  // 第二种写法
  let obj = {
    [symbol]: 'hello!'  // 方括号为ES6计算属性（computedPropertyName）
  }
  
  // 第三种写法
  let a = {}
  Object.defineProperty(a, symbol, {})
  Object.defineProperties()
  ```
  
  注意：`Symbol` 值作为对象属性名时，不能用点运算符（ES6的动态属性只在方括号中生效，而点`.`运算符后面总是字符串）
  
  ```JavaScript
  let sy = Symbol('feature')
  
  let obj = {
      [sy]: 'feature'
  }
  
  console.log(obj.sy) // undefined
  console.log(obj[sy]) // 'feature'
  ```
  
#### 3. 遍历属性名
  
  `Symbol` 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回
  
  ```JavaScript
  Object.getOwnPropertySymbols()
  Reflect.ownKeys() //Reflect.ownKeys()方法可以返回所有类型的键名，包括常规键名和 Symbol 键名
  ```
  
#### 4. API
 - `Symbol.prototype.description`
    
    ```JavaScript
    // 获取Symbol对象的描述
    let s = Symbol('feature')
    s.descriotion // "feature"
    ```
    
 
 - `Symbol.for()`：注意会被登记到全局环境，可以在不同的 `iframe` 或 `service worker` 中取到同一个值
    
    ```JavaScript
    Symbol.for("bar") === Symbol.for("bar") // true
    
    Symbol("bar") === Symbol("bar") // false
    ```
    
    注意：
    
    - `Symbol.for()`与`Symbol()`这两种写法，都会生成新的`Symbol`
    - `Symbol.for()`会被登记到全局环境，`Symbol()`不会
    - `Symbol.for()`会先检查给定的`key`是否已经存在,如果不存在则创建一个新的`Symbol`，存在就返回这个`Symbol`值
    
 - `Symbol.keyFor()`：返回被`Symbol.for()`注册到全局`Symbol`类型值的`key`
    
    ```JavaScript
    let s1 = Symbol.for("feature");
    Symbol.keyFor(s1) // "feature"
    
    let s2 = Symbol("feature");
    Symbol.keyFor(s2)  // undefined
    ```
    
#### 5. 常用实例

  - 消除魔术字符串：魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。风格良好的代码，应该尽量消除魔术字符串，改由含义清晰的变量代替
    
    ```JavaScript
    function fn(shape) {
      switch (shape) {
        case 'circle': // 魔术字符串
          break;
      }
    }
    
    // 常见解决方法：使用变量
    const shapeType = {
      circle: 'circle'
    }
    
    function fn(shape) {
      switch (shape) {
        case shapeType.circle: // 使用变量
          break;
      }
    }
    
    // 使用Symbol，定义的变量的属性值并不重要，只是为了区分
    const shapeType = {
      circle: Symbol() // 使用Symbol
    }
    ```
