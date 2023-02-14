---
title: Type 'string' is not assignable to type in TypeScript
date: 2022-6-23 17:50:28
update: 2022-6-24 15:47:34
tags:
  - Typescript
categories:
  - Typescript
description: "Type 'string' is not assignable to type in Typescript"
keywords: 'Typescript,const assertion,type assertion'
top_img: /assert/index-image.png
cover: false
---

## <center>Type 'string' is not assignable to type in Typescript</center>

#### Type 'string' is not assignable to type in Typescript
  当```Typescript```出现```"Type 'string' is not assignable to type"```错误时，一般情况是我们将```string```类型分配给了看起来是字符串类型，其实并不是完整的字符串类型。如```literal type(字面量)```或者```enum(枚举)```
  <br />
  解决这个问题其实比较简单，使用常量(const)定义或者类型断言

  如:
  ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

     // yellow: string
    let yellow = 'yellow'

    // Type 'string' is not assignable to type 'PrimaryColor'.
    const current: PrimaryColor = yellow
  ```

  在上例中，变量```yellow```类型为```string```,而```current```类型为```PrimaryColor```。当我们将```string```分配给```PrimaryColor```时就会出现此错误，这是因为，虽然```yellow```变量的值为```PrimaryColor```其中之一，但是它是```string```类型，对```PrimaryColor```来说太过广泛

  换句话来说，```PrimaryColor```只存在三个值```'red'```、```'yellow'```、```'green'```，而string类型可以为任意字符串

  解决办法：

  * 类型断言(type assertion)
  
    如上例，我们可以使用类型断言将```yellow```断言为```PrimaryColor```
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

     // yellow: PrimaryColor
    let yellow = 'yellow' as PrimaryColor

    const current: PrimaryColor = yellow
    ```
  * const断言(const assertion)

    使用const assertion将变量```yellow```断言为字符串字面量```'yellow'```
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

    // yellow: 'yellow'
    let yellow = 'yellow' as const

    const current: PrimaryColor = yellow
    ```

  * 使用const定义
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

    // yellow: 'yellow'
    const yellow = 'yellow'

    const current: PrimaryColor = yellow
    ```

  一些其他情况

  * 接下来看看另外一个例子
      ```typescript
      type Letter = 'a' | 'b'
      const arr: Letter[] = ['a', 'b']

      const obj: { letter: string } = {
        letter: 'a'
      }

      // Argument of type 'string' is not assignable to parameter of type 'Letter'.
      arr.push(obj.letter)
    ```
    因为数组```arr```类型为```Letter[]```,而```obj.letter```类型为```string```,因此发生错误。

    解决办法：
    ```typescript
      type Letter = 'a' | 'b'
      const arr: Letter[] = ['a', 'b']

      const obj: { letter: string } = {
        letter: 'a'
      }

      // type assertion
      arr.push(obj.letter as Letter)
    ```
    如果可以，最好将```obj.letter```在定义时直接定义为```Letter```类型

  * 还有一个例子
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

    // arr2: string[]
    const arr2 = ['yellow']

    const arr3: PrimaryColor[] = ['red', 'green']

    // Type 'string[]' is not assignable to type 'PrimaryColor[]'.
    // Type 'string' is not assignable to type 'PrimaryColor'.
    const arr4: PrimaryColor[] = [...arr2, ...arr3]
    ```
    原因是```arr2```类型为```string[]```，不能将其赋值给```PrimaryColor[]```，可以使用```const assertion```解决
    
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

    // arr2: readonly ["yellow"]
    const arr2 = ['yellow'] as const

    const arr3: PrimaryColor[] = ['red', 'green']

    const arr4: PrimaryColor[] = [...arr2, ...arr3]
    ```
    但是```const assertion```会将数组转化为只读元组类型，如果你后续想要修改数组中的类型将会发生错误，所以这种方法不适合数组需要修改的情况，可以使用```type assertion```解决
    ```typescript
    type PrimaryColor = 'red' | 'yellow' | 'green'

    // arr2: PrimaryColor[]
    const arr2 = ['yellow'] as PrimaryColor[]

    const arr3: PrimaryColor[] = ['red', 'green']

    const arr4: PrimaryColor[] = [...arr2, ...arr3]
    ```

#### Type string is not assignable to type Enum in Typescript
  另一种常见情况出现在枚举```Enum```
  ```typescript  
  enum PrimaryColor {
    red = 'red',
    yellow = 'yellow',
    green = 'green'
  }

  // Type '"yellow"' is not assignable to type 'PrimaryColor'.
  const color: PrimaryColor = 'yellow'
  ```
  解决办法
  ```typescript  
    enum PrimaryColor {
    red = 'red',
    yellow = 'yellow',
    green = 'green'
  }

  const color1: PrimaryColor = PrimaryColor.yellow

  const color2: PrimaryColor = 'yellow' as PrimaryColor
  ```

#### Type 'X' is not assignable to type 'Y' in Typescript
  这种情况出现赋值时左边的变量与右侧的值具有不兼容的类型，解决办法可以通过```type assertion(类型断言)```或```type guard(类型守卫)```在赋值之前比较两个值是否具有兼容性

  如下例：
  ```typescript
  // name: string
  let name = 'lemox'

  // Type 'number' is not assignable to type 'string'.
  name = 100
  ```
  变量```name```初始化时被typescript推断为```string```类型，当我们将```number```类型分配给```name```时就会发生错误。解决办法是确保两个值具有兼容性或者使用类型断言

  使用联合类型定义变量
  ```typescript
  // name: string | number
  let name: string | number = 'lemox'

  name = 100
  ```
  有时你可能知道两个值具有兼容性，但是typescript不知道，这时你可以使用```type assertion(类型断言)```

  还有
  ```typescript
  interface Person {
    name?: string
    country: string
  }

  const person: Person = {
    name: 'Bobby Hadz',
    country: 'Chile',
  }

  // Type 'string | undefined' is not assignable to type 'string'.
  // Type 'undefined' is not assignable to type 'string'.
  const name: string = person.name
  ```
  由于```person.name```为可选属性，即存在两种类型```string | undefined```，因此类型不兼容，可以使用类型断言或类型守卫解决
  
  * 类型断言
    ```typescript
    interface Person {
      name?: string
      country: string
    }

    const person: Person = {
      name: 'Bobby Hadz',
      country: 'Chile',
    }

    const name: string = person.name as string
    ```
    可以使用类型断言明确的告诉```typescript```，当前上下文```person.name```为```string```

  * 类型守卫
    更好的办法是使用类型守卫
    ```typescript
    interface Person {
      name?: string
      country: string
    }

    const person: Person = {
      name: 'Bobby Hadz',
      country: 'Chile',
    }

    const name: string = typeof person.name === 'string' ? person.name : ''
    ```
    这样可以确保我们总是给```name```分配了一个字符串


  * 还有一种常见错误
    ```typescript
    const arr = [1, 2, 3]

    const result = arr.pop()

    // Type 'number | undefined' is not assignable to type 'number'.
    const first: number = result
    ```
    因为```Array.pop()```方法的返回值可能为```undefined```，所以我们不能直接将其分配给```number```类型，可以使用以上提到的方式解决
    ```typescript
    const arr = [1, 2, 3]
    const result = arr.pop()

    // 类型断言
    const num1: number = result as number

    // 联合类型
    const num2: number | undefined = result

    // 类型守卫
    const num3: number = result ?? 0
    ```

#### 总结
  要解决```Type 'X' is not assignable to type 'Y'```问题，主要是需要确保两边值类型的兼容性，如果两个值兼容性不匹配，则会发生错误