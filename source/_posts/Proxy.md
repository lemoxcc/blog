---
title: Proxy
date: 2022-02-16 15:23:41
update: 2022-2-17 15:47:34
tags:
  - JavaScript
  - ES6
categories:
  - JavaScript
description: 'JavaScript Proxy'
keywords: 'JavaScript,Proxy,ES6'
top_img: /assert/index-image.png
cover: false
---

## <center>Proxy</center>

#### 1. `Proxy`对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义
  
  ```JavaScript
  const p = new Proxy(target, handler)
  ```
  
  - target
    
    要使用`Proxy`包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）
    
  - handler
    
    一个通常以函数作为属性的对象，各属性中的函数分别定义了在执行各种操作时代理 `p`的行为
    
    如果`handler`没有设置任何拦截行为，那就等同于直接通向原对象
  
#### 2. 要使得`Proxy`起作用，必须针对`Proxy`实例进行操作，而不是针对目标对象进行操作（可以将`Proxy`对象设置到对象`object.proxy`属性，从而可以在对象上调用）
  
  ```JavaScript
  var proxy = new Proxy({}, {
    get: function(target, propKey) {
      return 'proxy';
    }
  });
  
  let obj = Object.create(proxy);
  console.log(obj.time) // 'proxy'
  ```
  
  `proxy`对象是`obj`对象的原型，`obj`对象本身并没有`time`属性，所以根据原型链，会在`proxy`对象上读取该属性，导致被拦截
  
#### 3. `Proxy.revocable()`：创建一个可撤销的`Proxy`对象
  
  ```JavaScript
  const { proxy, revoke } = Proxy.revocable(target, handler)
  
  proxy // 对象：代理实例
  revoke() // 函数：取消代理，执行revoke()后再访问就会报错
  ```
  
#### 4. `this`问题
  
  在`Proxy`代理的情况下，目标对象内部的`this`关键字会指向`Proxy`代理，而拦截函数`this`指向`handler`对象
  
  ```JavaScript
  const target = {
    m: function () {
      console.log(this)
    }
  }
  
  const handler = {
    set(target, prop, value, receiver) {
      console.log(this) // hander
      target[prop] = value
      return true
    }
  }
  
  const proxy = new Proxy(target, handler);
  
  target.m() // target
  proxy.m()  // proxy
  proxy.name = 'lemon'
  ```
  
  此外，有些原生对象的内部属性，只有通过正确的`this`才能拿到，所以`Proxy`也无法代理这些原生对象的属性
  
  ```JavaScript
  const target = new Date()
  const handler = {}
  const proxy = new Proxy(target, handler)
  
  proxy.getDate()
  // TypeError: this is not a Date object.
  ```
  
#### 5. `handler`对象的方法（目前标准的是13种）
  - `get(target, prop, receiver)`：属性读取操作的捕捉器
  - `set(target, prop, value, receiver)`：属性设置操作的捕捉器，返回一个布尔值
  - `has(target, prop)`：`in`操作符的捕捉器，返回一个布尔值
  - `deleteProperty(target, property)`：`delete`操作符的捕捉器，返回一个布尔值
  - `ownKeys(target)`：`Object.getOwnPropertyNames`方法和 `Object.getOwnPropertySymbols`方法的捕捉器，返回一个数组
  - `getOwnPropertyDescriptor(target, prop)`：`Object.getOwnPropertyDescriptor`方法的捕捉器，返回属性的描述对象
  - `defineProperty(target, property, descriptor)`：`Object.defineProperty`方法的捕捉器，返回一个布尔值
  - `preventExtensions(target)`：`Object.preventExtensions`方法的捕捉器，返回一个布尔值
  - `getPrototypeOf(target)`：`Object.getPrototypeOf`方法的捕捉器，返回一个布尔值
  - `setPrototypeOf(target, prototype)`：`Object.setPrototypeOf`方法的捕捉器，返回一个布尔值
  - `isExtensible(target)`：`Object.isExtensible`方法的捕捉器，返回一个布尔值
  - `apply(target, thisArg, argumentsList)`：函数调用操作的捕捉器
  - `construct(target, argumentsList, newTarget)`：`new`操作符的捕捉器，返回一个对象
  
#### 6. 实例
  - `get(target, prop, receiver)`
  
    ```JavaScript
    type Params = { target: '目标对象', prop: '属性名', receiver: 'Proxy或者继承Proxy的对象' }
    ```
    
    注意：
    
    - 如果目标对象的属性不可配置`configurable: false`且不可写`writable: false`，则`Proxy`不能修改该属性，通过`Proxy`访问该属性会报错
    - 如果目标属性没有配置访问方法，即get方法为`undefined`，则返回值必须为`undefined`
  
  - `set(target, prop, value, receiver)`
    
    ```JavaScript
    type Params = { target: '目标对象', prop: '属性名', value: '属性值', receiver: 'Proxy实例本身' }
    ```
    
    注意：
    
    - 如果目标对象的属性不可写`writable: false`，则`set`方法无效
    - 返回`Boolean`值，`true`设置成功，`false`设置失败（严格模式`false`会报错）
    - 如果目标属性没有配置储存方法，即`set`方法为`undefined`，则不能设置值
    
  - `apply(target, thisArg, argumentsList)`
    
    ```JavaScript
    type Params = { target: '目标对象', thisArg: '目标对象的上下文对象（this）', argumentsList: '参数数组' }
    
    const twice = {
      apply (target, thisArg, argumentsList) {
        return Reflect.apply(...arguments) * 2
      }
    }
    
    function sum (left, right) {
      return left + right
    }
    
    const proxy = new Proxy(sum, twice)
    proxy(1, 2) // 6
    proxy.call(null, 5, 6) // 22
    proxy.apply(null, [7, 8]) // 30
    ```
    
    注意：
    
    - `target`必须是可以被调用的，即必须是一个函数对象
    
  - `has(target, prop)`
    
    ```JavaScript
    type Params = { target: '目标对象', prop: '属性名' }
    ```
    
    注意：
    
    - 如果目标对象不可扩展，使用`has`拦截就会报错
    - 如果目标对象的某一属性本身不可被配置，使用`has`拦截就会报错
    
  - `construct(target, argumentsList, newTarget)`
    
    ```JavaScript
    type Params = { target: '目标对象（函数）', argumentsList: '参数数组', newTarget: 'new 命令作用的构造函数' }
    ```
    
    注意：返回值必须是一个对象，否则会报错，由于`construct()`拦截构造函数，所以目标对象必须是函数
    
  - `deleteProperty(target, property)`
    
    ```JavaScript
    type Params = { target: '目标对象', property: '属性名' }
    ```
    
    注意：
    
    - `deleteProperty`必须返回一个 `Boolean`类型的值，表示了该属性是否被成功删除
    - 如果目标对象属性不可配置`configurable: false`，不能被删除，就会报错
    
  - `defineProperty(target, property, descriptor)`
    
    ```JavaScript
    type Params = { target: '目标对象', property: '属性名', descriptor: '待定义或修改的属性的描述符' }
    ```
    
    注意：
    
    - `defineProperty`方法必须以一个 `Boolean`返回，表示定义该属性的操作成功与否（严格模式返回`false`会报错）
    - 如果目标对象不可扩展（`non-extensible`），则`defineProperty()`不能增加目标对象上不存在的属性，否则会报错
    - 如果目标对象属性不可写`writable`或不可配置`configurable`，则`defineProperty()`方法不能改变这两个设置
    
  - `getOwnPropertyDescriptor(target, prop)`
    
    ```JavaScript
    type Params = { target: '目标对象', prop: '属性名' }
    ```
    
    注意：
    
    - `getOwnPropertyDescriptor`必须返回一个`object`或`undefined`
    
  - `getPrototypeOf(target)`
    
    ```JavaScript
    type Params = { target: '目标对象' }
    ```
    
    注意：
    
    - `getPrototypeOf()`必须返回对象或者`null`，否则会报错
    - 如果目标对象不可扩展（`non-extensible`），`getPrototypeOf()`方法必须返回目标对象的原型对象
    
  - `isExtensible(target)`
    
    ```JavaScript
    type Params = { target: '目标对象' }
    ```
    
    注意：
    
    - `isExtensible()`必须返回布尔值，否则返回值会被自动转为布尔值，且返回值必须与目标对象的`isExtensible`属性保持一致，否则会保持
    
  - `ownKeys(target)`
    
    ```JavaScript
    type Params = { target: '目标对象' }
    ```
    
    注意：
    
    - 使用`Object.keys()`方法时，有三类属性会被`ownKeys()`自动过滤，不会返回（1. 不存在的属性 2. Symbol属性 3. 不可遍历`enumerable`属性）
    - `ownkeys()`方法返回的数组成员，只能是字符串或`Symbol`值，其他类型或者根本不是数组就会报错
    - 如果目标对象包含不可配置的属性，则该属性必须被`ownkeys()`返回，否则会报错
    - 如果目标对象不可扩展（`non-extensible`），`ownkeys()`必须返回原对象的所有属性，且不能包含多余的属性，否则会报错
    
  - `preventExtensions(target)`
    
    ```JavaScript
    type Params = { target: '目标对象' }
    ```
    
    注意：
    
    - `preventExtensions()`有一个限制，只有当目标对象不可扩展时（即`Object.isExtensible(proxy)`为`false`），`proxy.preventExtensions`只能返回`true`，否则会报错
    - `preventExtensions()`返回一个布尔值
    
  - `setPrototypeOf(target, prototype)`
    
    ```JavaScript
    type Params = { target: '目标对象', prototype: '对象新原型或null' }
    ```
    
    注意：
    
    - 如果修改成功， `setPrototypeOf`方法返回 `true`，否则返回 `false`
    - 如果`target`不可扩展，原型参数必须与`Object.setPropertyOf(target)`的值相同
