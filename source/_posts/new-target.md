---
title: new.target
date: 2021-12-12 15:47:34
update: 2021-12-12 15:47:34
tags:
  - JavaScript
  - ES6
categories:
  - JavaScript
description: 'new.target'
keywords: 'new.target,JavaScript,ES6'
top_img: /assert/index-image.png
cover: false
---

## <center>new.target</center>

#### 1. 定义
> `new.target`属性（ES6引入）允许你检测函数或构造方法是否通过`new`运算符进行调用。通过new运算符被初始化的函数或构造方法时，`new.target`返回一个指向构造方法或函数的引用，在普通的函数调用中，`new.target`返回`undefined`
    
  注意：
  
  - 在`arrow function`中，没有自己的`this`、`arguments`、`super`、`new.target`，`new.target`指向最近的外层函数的`new.target`
  - 在子类继承父类时，`new.target`指向初始化类的类定义，即子类
    
#### 2. 在ES5中如何限制函数通过`new`运算符调用
  - 通过`new`运算符调用构造函数，`this`将指向构造函数实例
  - 普通调用函数，非严格模式`this`指向`window`，严格模式指向`undefined`
      
      ```typescript
      function Instance() { return this }
      
      // 普通调用
      console.log(Instance())  // Window {...}
      
      // 通过new调用
      console.log(new Instance()) // Instance {}
      ```
      
  
  - 因此我们可以通过判断`this`是否当前构造函数的实例，来判断是否通过`new`运算符调用，使用`this instanceof [构造函数]`，通过`new`调用返回`true`，普通调用返回`false`
      
      ```typescript
      function Instance() {
        // 不建议使用 arguments.callee.name，在严格模式下arguments属性被移除
        if(!(this instanceof Instance)) {
          throw new TypeError("Failed to construct 'Instance': Please use the 'new' operator, this DOM object constructor cannot be called as a function.")
        }
      }
      
      // 普通调用
      console.log(Instance()  // Uncaught TypeError: ...
      
      // 通过new调用
      console.log(new Instance()) // Instance {}
      ```
      
  - 但通过此方法判断并不完全准确，如果调用时通过`apply`/`call`/`bind`改变`this`指向，将`this`指向构造函数实例，以上方法将无效
      
      ```typescript
      console.log(Instance.call(new Instance()))  // undefined
      ```
      
#### 3. new.target
  - 通过`new`运算符调用构造函数，`new.target`指向构造方法或函数的引用
  - 普通调用函数，`new.target`指向`undefined`
      
      ```typescript
      function Instance() { return new.target }
      
      // 普通调用
      console.log(Instance())  // undefined
      
      // 通过new调用
      console.log(new Instance()) // Instance() { return new.target }
      ```
      
  - 因此我们可以使用`new.target`来限制构造函数必须通过`new`运算符调用
      
      ```typescript
      function Instance() {
        if(!new.target) {
          throw new TypeError("Failed to construct 'Instance': Please use the 'new' operator, this DOM object constructor cannot be called as a function.")
        }
      }
      
      // 普通调用
      console.log(Instance())  // Uncaught TypeError: ...
      
      // 改变this指向
      console.log(Instance.call(new Instance())) // Uncaught TypeError: ...
      
      // 通过new调用
      console.log(new Instance()) // Instance() { return new.target }
      ```
      
#### 4. 使用`new.target`模拟实现抽象类
  - 因为当子类继承父类时，`new.target`指向初始化类的类定义
      
      ```typescript
      class P { constructor() { console.log(new.target) } }
      
      class S extends P { constructor() { super() } }
      
      new P() // class P { constructor() { console.log(new.target) } }
      new S() // class S extends P { constructor() { super() } }
      ```
      
  - 利用这一特性，可以模拟实现抽象类
      
      ```typescript
      class P {
        constructor() {
          if(new.target === P) {
            throw new SyntaxError('Cannot create an instance of an abstract class.')
          }
        }
      }
      
      class S extends P {
        constructor() {
          super()
        }
      }
      
      console.log(new P()) // Uncaught SyntaxError: Cannot create an instance of an abstract class.
      console.log(new S()) // S {}
      ```
      
#### 5. 总结
  - 在ES5中，通过`new`运算符调用时`this`指向构造函数实例，普通调用时`this`在非严格模式指向`window`，严格模式指向`undefined`，利用这一特性，结合`this instanceof [构造函数]`，可以判断是否通过`new`运算符调用构造函数，但此方法并不安全，可以通过`apply`/`call`/`bind`改变`this`指向
  - 在ES6中，引入`new.target`属性，通过`new`运算符调用时`new.target`指向构造方法或函数的引用，普通调用函数，`new.target`指向`undefined`，可以通过此属性判断是否通过`new`运算符调用构造函数
  - 利用`new.target`属性，可以模拟实现抽象类
