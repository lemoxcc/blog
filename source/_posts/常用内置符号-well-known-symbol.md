---
title: 常用内置符号(well-known symbol)
date: 2021-03-17 21:18:38
update: 2021-4-12 15:47:34
tags:
  - JavaScript
categories:
  - JavaScript
description: '常用内置符号(well-known symbol)'
keywords: '常用内置符号(well-known symbol),JavaScript,Symbol'
top_img: /assert/index-image.png
cover: false
---

## <center>常用内置符号(well-known symbol)</center>
  ES6引入了一批常用内置符合（well-known symbol）用于暴露语言内部行为，开发者可以直接访问、重写或模拟这些行为

  - 内置符号以Symbol工厂函数字符串属性的形式存在
  - 内置符号的主要用途之一是**重新定义**，从而改变原生结构的行为
  - 内置符号没有特别之处，它们就是全局函数Symbol的普通字符串属性，指向一个符号的实例
  - **所有内置符号属性都是不可写（writeable: false），不可枚举（enumerable: false），不可配置（configurable: false）**

  **目前公有13个内置符号**

#### **`Symbol.Iterator`**
    
  `Symbol.iterator`为每一个对象定义了默认的迭代器。
    
  示例：
    
    ```tsx
      class Range {
        constructor(max) {
          this.max = max
          this.idx = 0
        }
      
        *[Symbol.iterator]() {
          while(this.idx < this.max) {
            yield this.idx++
          }
        }
      }
      
      const range = new Range(5)
      console.log(...range) // 0 1 2 3 4
    ```
    
  一些拥有默认定义迭代器的内置类型：**`Array`、`TypedArray`、`String`、`Map`、`Set`**

#### **`Symbol.asyncIterator`**
    
  `Symbol.asyncIterator`符号指定了一个对象的默认异步迭代器
    
  示例：
    
    ```tsx
      class Range {
        constructor(max) {
          this.max = max
          this.idx = 0
        }
      
        async *[Symbol.asyncIterator]() {
          while(this.idx < this.max) {
            yield Promise.resolve(this.idx++)
          }
        }
      }
      
      (async () => {
        for await(const r of new Range(5)) {
          console.log(r) // 0 1 2 3 4
        }
      })()
    ```
    
#### **`Symbol.hasInstance`**
    
  `Symbol.hasInstance`用于判断某对象是否为某构造器的实例。因此你可以用它自定义 [`instanceof`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)操作符在某个类上的行为。
    
  ES6中，`instanceof`操作符会使用`Symbol.hasInstance`函数来确定关系，所以直接调用`Symbol.hasInstance`与使用`instanceof`操作符是一样的
    
    ```tsx
      const a = []
      console.log(Array[Symbol.hasInstance](a)) // true
      console.log(a instanceof Array) // true
      ```
      
  `Symbol.hasInstance`属性定义在`Function`原型上，因此默认所有函数和类上都可以使用。也可以在类上**通过静态方法重新定义**这个函数
      
  `Symbol.hasInstance`返回一个`Boolean`, 如果返回的不是`Boolean`, 会将`Flasy`转换为`false`, 其余为`true`
      
      ```tsx
      class Surpass10 {
        // 注意要使用静态方法，否则无法访问到此方法
        // Symbol.hasInstance返回一个Boolean, 如果返回的不是Boolean, 会将Flasy转换为false, 其余为true
        static [Symbol.hasInstance](value) {
          if(Number.isFinite(value) && value > 10) {
            return true
          }
      
          return false
        }
      }
      
      console.log(12 instanceof Surpass10) // true
      console.log(7 instanceof Surpass10) // false
    ```
    
#### **`Symbol.isConcatSpreadable`**
    
  `Symbol.isConcatSpreadable`符号用于配置某对象作为`Array.prototype.concat()`方法的参数时是否展开其数组元素
    
  数组默认`true`，类数组（`ArrayLike`）默认`false`
    
    ```tsx
      const a = [1, 2, 3]
      const b = [3, 4, 5]
      
      console.log(a.concat(b)) // [ 1, 2, 3, 3, 4, 5 ]
      
      b[Symbol.isConcatSpreadable] = false
      
      console.log(a.concat(b)) // [ 1, 2, 3, [ 3, 4, 5, [Symbol(Symbol.isConcatSpreadable)]: false ] ]
    ```
    
#### **`Symbol.match`**
    
  `Symbol.match`指定了匹配的是正则表达式而不是字符串。`String.prototype.match()`方法会调用此函数
    
    ```tsx
      class FooMatcher {
        static [Symbol.match](target) {
          // String.includes 用于查找字符串是否包含此字符串，返回布尔值
          return target.includes('foo')
        }
      }
      
      console.log('foobar'.match(FooMatcher)); // true 
      console.log('barbaz'.match(FooMatcher)); // false 
      
      /* 重新定义Symbol.match() 函数 */
      class StringMatcher {
        constructor(str) {
          this.str = str
        }
        [Symbol.match](target) {
          return target.includes(this.str)
        }
      }
      
      // 第一个String为总字符串，第二个String为要查找的字符
      console.log('foobar'.match(new StringMatcher('foo'))); // true 
      console.log('barbaz'.match(new StringMatcher('qux'))); // false
    ```
    
#### **`Symbol.matchAll`**
    
  `String.prototype.matchAll()`方法会调用此函数
    
    ```tsx
      const str = "2016-01-02|2019-03-07"
      const numbers = {
        *[Symbol.matchAll](str) {
          for (const n of str.matchAll(/[0-9]+/g)) yield n[0]
        }
      }
      
      console.log(Array.from(str.matchAll(numbers))) // [ '2016', '01', '02', '2019', '03', '07' ]
    ```
    
#### **`Symbol.replace`**
    
  `Symbol.replace`这个属性指定了当一个字符串替换所匹配字符串时所调用的方法。`String.prototype.replace()`方法会调用此方法
    
  `Symbol.replace`接收两个参数，调用 replace()方法的字符串实例和替换字符串
    
    ```tsx
      class Replace1 {
        constructor(value) {
          this.value = value
        }
        [Symbol.replace](string, replace) {
          return `s/${string}/${this.value}/${replace}/g`
        }
      }
      
      console.log('foo'.replace(new Replace1('bar'), 'zoo')) // s/foo/bar/zoo/g
    ```
    
#### **`Symbol.search`**
    
  `Symbol.search` 指定了一个搜索方法，这个方法接受用户输入的正则表达式，返回该正则表达式在字符串中匹配到的下标，这个方法由以下的方法来调用 `String.prototype.search()`
    
    ```tsx
      class caseInsensitiveSearch {
        constructor(value) {
          this.value = value.toLowerCase()
        }
        [Symbol.search](string) {
          return string.toLowerCase().indexOf(this.value)
        }
      }
      
      console.log('foobar'.search(new caseInsensitiveSearch('BaR'))) // 3
    ```
    
#### **`Symbol.species`**
    
  `Symbol.species` 符号属性表示“一个函数值，该函数作为创建派生对象的构造函数”；这个属性在内置类型中最常用，用于对内置类型实例方法的返回值暴露实例化派生对象的方法
    
    ```tsx
      class MyArray extends Array {
        // 覆盖 species 到父级的 Array 构造函数上
        static get [Symbol.species]() { return Array }
      }
      var a = new MyArray(1,2,3)
      var mapped = a.map(x => x * x)
      
      console.log(mapped instanceof MyArray) // false
      console.log(mapped instanceof Array)   // true
    ```
    
#### **`Symbol.split`**
    
  `Symbol.split`指向 一个正则表达式的索引处分割字符串的方法。这个方法通过 `String.prototype.split()`调用
    
  `Symbol.split`函数接收一个参数，调用`match()`方法的字符串实例
    
    ```tsx
      class Split1 {
        constructor(value) {
          this.value = value
        }
        [Symbol.split](string) {
          const index = string.indexOf(this.value);
          return `${this.value}${string.substr(0, index)}/${string.substr(index + this.value.length)}`
        }
      }
      
      console.log('foobar'.split(new Split1('foo')))
    ```
    
#### **`Symbol.toPrimitive`**
    
  `Symbol.toPrimitive`是内置的 symbol 属性，其指定了一种接受首选类型并返回对象原始值的表示的方法。它被所有的强类型转换制算法优先调用
    
  在 `Symbol.toPrimitive`属性（用作函数值）的帮助下，对象可以转换为一个原始值。该函数被调用时，会被传递一个字符串参数 `hint`，表示要转换到的原始值的预期类型。`hint`参数的取值是 `"number"`、`"string"`和 `"default"`中的任意一个
    
    ```tsx
      const obj = {
        [Symbol.toPrimitive](hint) {
          if (hint === "number") {
            return 10
          }
          if (hint === "string") {
            return "hello"
          }
          return true
        }
      };
      console.log(+obj) // 10  — hint 参数值是 "number"
      console.log(`${obj}`) // "hello"   — hint 参数值是 "string"
      console.log(obj + '') // "true"    — hint 参数值是 "default"
    ```
    
#### **`Symbol.toStringTag`**
    
  `Symbol.toStringTag`用于创建对象的默认字符串描述。它由 `Object.prototype.toString()`方法内部访问
    
    ```tsx
      class ValidatorClass {
        get [Symbol.toStringTag]() {
          return 'Validator'
        }
      }
      
      console.log(Object.prototype.toString.call(new ValidatorClass())) // '[object Validator]'
    ```
    
#### **`Symbol.unscopables`**
    
  `Symbol.unscopables`指用于指定对象值，其对象自身和继承的从关联对象的 with 环境绑定中排除的属性名称
    
    ```tsx
      const object1 = {
        property1: 42,
        property2: 42
      }
      
      object1[Symbol.unscopables] = {
        property2: true
      }
      
      with (object1) {
        console.log(property1) // 42
        console.log(property2) // Expected output: Error: property1 is not defined
      }
    ```
