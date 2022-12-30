---
title: Index Signature
date: 2021-12-12 15:47:34
update: 2021-12-12 15:47:34
tags:
  - Typescript
categories:
  - Typescript
description: 'Index Signature'
keywords: 'Index Signature,Typescript,TS'
top_img: /assert/index-image.png
cover: false
---

## <center>Index Signature</center>

#### 1. 序言
  你有两个对象，分别描述了两个工作人员的工资：
  ```TS
  const salary1 = {
    baseSalary: 10,
    yearlyBonus: 20
  }
  
  const salary2 = {
    contractSalary: 11
  }
```

  之后，你想实现一个函数，根据工作人员的工资信息得到总薪资

  ```TS
  function totalSalary(salaryObject: ???) {
    let total = 0;
    for (const name in salaryObject) {
      total += salaryObject[name];
    }
    return total;
  }

  totalSalary(salary1); // 30
  totalSalary(salary2); // 11
  ```

  看起来函数似乎可以正常工作，`salaryObject`被`typescript`推断为any，这时候我们可以传入任何对象。当我们传入的对象值的类型不为`number`时，此函数将会在运行时报错（比如：`{ baseSalary: true }`）

  这个时候应该如果定义`totalSalary`函数的参数`totalSalary`的类型注解呢？答案是`Index Signature`（索引签名），接下来我们来看看如何在`Typescript`中使用索引签名，以及何时需要它

#### 2. 什么是索引签名？
    
  当你不确定对象属性的名称，只知道属性和值的类型，这时可以使用索引签名来描述。
    
  比如`salaryObject`，我们可以确定属性的类型（`string`）、值的类型（`number`），但我们不知道具体的属性名。便可以使用索引签名：
    
  ```TS
  type salaryType = {
    [key: string]: number
  }
  ```
    
  此类型描述了一个以字符串类型作为属性，数字类型作为值的对象。当我们传入非`number`类型的属性值对象时，`typescript`将会提示错误

#### 3. 索引签名语法

  索引签名语法非常简单，只需要在方括号内写下属性的类型：`{ [key: keyType]: valueType }`，一些例子：
    
  ```TS
  type Is = {
    [key: string]: string
  }
  
  type Iss = {
    [key: string | number]: string | number
    name: string
  }
  ```
  
  在第二个例子`Iss`中，我们还定义了一个`name`属性，标识此类型对象上必须要有一个`name`属性，但这里有一个限制，索引签名强制要求所有的属性值必须为索引签名的属性值相匹配（或子类型），否则，`Typescript`将会提示错误：
  
  ```TS
  type Iss = {
    [key: string | number]: string | number
    ~~name~~: boolean // Error: Property 'name' of type 'boolean' is not assignable to 'string' index type 'string | number'.
  }
  ```
  
  注意，索引签名属性只允许使用：string、number、symbol、template string（模板字符串）以及只包含这些类型的联合类型
  
  ```TS
  type Is = {
    [key: ~~boolean~~]: string // Error: An index signature parameter type must be 'string', 'number', 'symbol', or a template literal type.
  }
  ```

#### 4. 索引签名注意事项
  - 访问一个不存在的对象
    
    当我们视图访问索引签名`{ [key: string]: string }`对象上不存在的对象时，会发生什么？
    
    ```TS
    type Is = {
      [key: string]: string
    }
    
    let obj: Is = {}
    
    obj.a // string
    ```
    
    正如索引签名定义的那样，`Typescript`将会把`obj.a`推断为`string`，但我们知道，在运行时`obj.a`实际为`undefined`
    
    索引签名只会将属性类型映射到值类型，值类型可能会偏离实际允许时的数据，这部分需要根据实际代码自行处理。当然，你也可以将值类型增加`undefined`类型，这样`Typescript`会提示你访问的属性可能不存在。
    
    ```TS
    type Is = {
      [key: string]: string | undefined
    }
    
    let obj: Is = {}
    
    obj.a // string | undefined
    ```
    
- `string`和`number`类型属性
    
    当我们定义一个以数字为`key`的对象，我们可以直接定义为：`[key: string]: string`，如：
    
    ```TS
    type Is = {
      [key: string]: string
    }
    
    let obj: Is = {
      1: 'one'
    }
    
    // 可以通过 obj['1'] 或 obj[1] 访问
    const val1 = obj['1']
    const val2 = obj[1]
    ```
    
    为什么我们定义时`key`的类型为`string`，但确可以通过`number`访问，`Typescript`也不会提示错误呢？
    
    因为`JavaScript`在访问对象时，会隐式的将数字强制转换为字符串（`obj[1]`→ `obj['1']`），这意味着访问`obj[1]`和`obj['1']`是一样的，`Typescript`也执行这种隐式转换。因此我们可以同时定义两种类型的索引签名：
    
    ```TS
    type Is = {
      [key: string]: string
      [index: number]: string
    }
    ```
    
    同样也要注意，数字类型属性值必须与字符串类型属性值的相匹配（或子类型）
    
    ```TS
    type Is = {
      [key: string]: string | number
      [index: number]: ~~boolean~~ // Error: 'number' index type 'boolean' is not assignable to 'string' index type 'string | number'.
    }
    ```

#### 5. 索引签名与Record
  
  `Typescript`提供了一个实用类型`Record`：
  
  ```TS
  const obj1: Record<string, string> = { prop: 'Value' }
  const obj2: { [key: string]: string } = { prop: 'Value' }
  ```
  
  它与索引签名类似，那么我们如何区分何时使用索引签名，何时使用`Record`呢？
  
  正如我们前面所提到的，索引签名对属性类型定义存在限制（索引签名属性类型只允许：string、number、symbol、template string）。当我们尝试在索引签名字面量类型或字面量联合类型，`Typescript`将会提示错误：
  
  ```TS
  type Is = {
    [~~key~~: 'name' | 'hobby']: string
  }
  // Error: An index signature parameter type cannot be a literal type or generic type. Consider using a mapped object type instead.
  ```
  
  但我们可以通过`Record`定义：
  
  ```TS
  type Is = Record<'name' | 'hobby', string>
  
  const obj: Is =  {
    name: 'lemon',
    hobby: 'program'
  }
  ```
  
  所以，建议使用索引签名来注释通用对象（当我们只知道属性的类型和属性值的类型时）。但当我们知道具体的属性时，建议使用`Record<keys, Type>`来注释特定对象
  
#### 6. 总结

 - 索引签名的语法：`[key: KeyType]: ValueType`，其中`KeyType`类型只允许：`string、number、symbol、template string`，而`ValueType`可以是任何类型
 - 当你不知道要注释的对象的具体结构（具体的属性名），但你知道属性名的类型和属性值的类型，那你可能需要的就是索引签名