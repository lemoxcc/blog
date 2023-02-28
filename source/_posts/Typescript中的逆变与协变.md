---
title: Typescript中的逆变与协变
date: 2022-04-16 20:48:19
update: 2022-4-27 15:47:34
tags:
  - Typescript
categories:
  - Typescript
description: 'Typescript中的逆变与协变'
keywords: 'Typescript,逆变,协变'
top_img: /assert/index-image.png
cover: false
---

## <center>Typescript中的逆变与协变</center>
  TypeScript 是一门静态类型语言，具有丰富的类型系统特性，其中逆变和协变是两个重要的概念。它们在 TypeScript 中用于描述函数类型、泛型和类继承等场景中的类型转换关系

#### 什么是逆变和协变
  在 TypeScript 中，逆变和协变是两个类型转换的方向。逆变用于从更具体的类型转换为更一般的类型，而协变则用于从更一般的类型转换为更具体的类型
  
  换句话说，逆变和协变都是用来描述类型之间的包含关系。逆变意味着一个更具体的类型可以替代一个更一般的类型，而协变意味着一个更一般的类型可以替代一个更具体的类型

  例如，我们有两个类型 A 和 B，如果 A 是 B 的子类型，那么 A 就是更具体的类型，B 就是更一般的类型。逆变和协变分别描述了从 A 到 B 和从 B 到 A 的类型转换关系

#### TypeScript中的逆变
  在 TypeScript 中，逆变通常发生在函数参数的类型转换中。当函数参数的类型需要比函数签名中声明的类型更具体时，就需要使用逆变

  例如，我们有一个函数 f，它接收一个类型为 B 的参数，并返回一个类型为 A 的结果。如果我们要将这个函数赋值给一个变量 g，并且这个变量需要接收一个类型为 A 的参数，那么我们需要使用逆变。我们可以将 f 函数的参数类型从 B 转换为 A 的超类型，以满足变量 g 的要求

  ```typescript
  interface A {
    name: string
  }

  interface B extends A {
    age: number
  }

  function f(b: B): A {
    return { name: b.name }
  }

  const g: (a: A) => A = f // 使用逆变，将 B 转换为 A 的超类型
  ```

  在上面的代码中，我们定义了两个接口 A 和 B，其中 B 继承自 A。函数 f 接收一个类型为 B 的参数，并返回一个类型为 A 的结果。我们将这个函数赋值给变量 g，并且需要将 B 类型的参数转换为 A 类型，以满足 g 变量的类型要求

#### TypeScript中的协变
  协变通常发生在函数返回值的类型转换中。当函数返回值的类型需要比函数签名中声明的类型更具体时，就需要使用协变

  例如，我们有一个函数 f，它返回一个类型为 A 的结果，但实际上它返回的是一个类型为 B 的实例，其中 B 是 A 的子类型。如果我们要将这个函数赋值给一个变量 g，并且这个变量需要返回一个类型为 B 的结果，那么我们需要使用协变。我们可以将 f 函数的返回值类型从 A 转换为 B 的子类型，以满足变量 g 的要求

  ```typescript
  interface A {
    name: string
  }

  interface B extends A {
    age: number
  }

  function f(): A {
    return { name: "foo" }
  }

  const g: () => B = f // 使用协变，将 A 转换为 B 的子类型
  ```
  在上面的代码中，我们定义了两个接口 A 和 B，其中 B 继承自 A。函数 f 返回一个类型为 A 的结果，实际上它返回的是一个类型为 B 的实例。我们将这个函数赋值给变量 g，并且需要将返回值类型从 A 转换为 B 的子类型，以满足 g 变量的类型要求

#### TypeScript中的泛型逆变和协变
  在 TypeScript 中，逆变和协变也可以应用于泛型类型参数。当一个泛型类型参数需要比其声明的类型更具体时，就需要使用逆变；当一个泛型类型参数需要比其声明的类型更一般时，就需要使用协变

  例如，我们有一个泛型函数 f，它接收一个类型为 `Array<B>` 的数组，并返回一个类型为 `Array<A>` 的数组。如果我们要将这个函数赋值给一个变量 g，并且这个变量需要接收一个类型为 `Array<A>` 的数组，那么我们需要使用逆变。我们可以将泛型类型参数 T 的类型从 `Array<B>` 转换为 `Array<A>` 的超类型，以满足变量 g 的要求
  
  ```typescript
  interface A {
    name: string
  }

  interface B extends A {
    age: number
  }

  function f<T extends B>(arr: Array<T>): Array<A> {
    return arr.map(item => ({ name: item.name }))
  }

  const g: <T extends A>(arr: Array<T>) => Array<A> = f // 使用逆变，将 Array<B> 转换为 Array<A> 的超类型
  ```
  在上面的代码中，我们定义了两个接口 A 和 B，其中 B 继承自 A。泛型函数 f 接收一个类型为 `Array<B>` 的数组，并返回一个类型为 `Array<A>` 的数组。我们将这个函数赋值给变量 g，并且需要将泛型类型参数 T 的类型从 `Array<B>` 转换为 `Array<A>` 的超类型，以满足变量 g 的类型要求

  另外，需要注意的是，在 TypeScript 中，函数参数和返回值是可以同时具有逆变和协变的

  例如，我们有一个函数类型 Func，它接收一个类型为 `Array<A>` 的数组，并返回一个类型为 `Array<B>` 的数组。如果我们要将这个函数类型赋值给一个变量 f，并且这个变量需要接收一个类型为 `Array<B>` 的数组，并返回一个类型为 `Array<A>` 的数组，那么我们需要同时使用逆变和协变

  ```typescript
  interface A {
    name: string
  }

  interface B extends A {
    age: number
  }

  type Func = (arr: Array<A>) => Array<B>

  const f: (arr: Array<B>) => Array<A> = (arr: Array<B>): Array<A> =>
    arr.map(item => ({ name: item.name }))

  const g: Func = f // 同时使用逆变和协变，将参数类型从 Array<B> 转换为 Array<A>，将返回值类型从 Array<A> 转换为 Array<B>
  ```

  在上面的代码中，我们定义了两个接口 A 和 B，其中 B 继承自 A。函数类型 Func 接收一个类型为 `Array<A>` 的数组，并返回一个类型为 `Array<B>` 的数组。我们将函数 f 赋值给变量 g，并且需要同时使用逆变和协变，以满足变量 g 的类型要求

#### 总结
  在 TypeScript 中，逆变和协变是非常有用的类型系统特性。它们可以帮助我们更好地理解和设计函数和类型。逆变和协变的区别在于，逆变是指一个类型可以被替换为其超类型，而协变是指一个类型可以被替换为其子类型。在 TypeScript 中，逆变和协变可以应用于函数参数类型、函数返回值类型和泛型类型参数

  使用逆变和协变可以帮助我们编写更灵活、更通用的代码，同时也可以帮助我们更好地理解和设计接口和类型。在使用逆变和协变时，我们需要注意类型安全性和代码的可读性，以避免出现潜在的错误和困惑

  希望这篇博客能够帮助你更好地理解 TypeScript 中的逆变和协变，并在实际开发中应用它们