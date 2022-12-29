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

  看起来函数似乎可以正常工作，`salaryObject`被`typescript`推导为any，这时候我们可以传入任何对象。当我们传入的对象值的类型不为`number`时，此函数将会在运行时报错（比如：`{ baseSalary: true }`）

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