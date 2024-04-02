---
date: 2023-11-12 21:57:52
url: 
aliases:
title: TypeScript基础
---
## 环境搭建

### 编译环境

* 全局安装：`npm install typescript -g`
* 查看版本：`tsc --version`
* 编译：`tsc ./hello.ts`
* 简化我们每次编写都要编译的步骤：
  * 方式一：使用webpack
  * 方式二：使用ts-node库

### 使用ts-node库

* 全局安装：`npm install ts-node -g`
* 同时我们的ts-node还依赖两个包，我们一起安装：`npm install tslib @types/node -g`
* 使用：`ts-node ./test.ts`

### TS配置文件

生成配置文件：`tsc --init`

## TS类型

### 变量声明

* 完整的声明格式：
  * 声明了类型后TypeScript就会进行类型检测，声明的类型可以称之为类型注解（Type Annotation) ;
  * `let/const/var 标识符:数据类型=赋值【let a:string="Hello TypeScript"】`
* 注意：ts中声明类型的string是小写的，和String是不一样的

### 变量的类型推导

如果没有明确的指定类型，那么 TypeScript 会依照类型推论（Type Inference）的规则推断出一个类型。

```js
let weather = "cloudy"; //等价于 let weather:string="cloudy"

//类型推导出weather是tring类型，不能将number赋值给它
// weather = 18

// 如果定义的时候没有赋值，那么不管以后何时赋值，变量都会被推断为any类型而避免类型检查
let a;
a = "hhh";
a = 18;
```

* 声明一个标识符时，如果有直接进行赋值，会根据赋值的类型推导出标识符的类型注解，这个过程称之为类型推导
  * let进行类型推导，推导出来的是通用类型
  * const进行类型推导，推导出来的是字面量类型

### js与ts数据类型

两者大部分数据类型包括number、boolean、string、Symbol、null、undefined等是一样的，下面是有所区别的ts数据类型

### Array类型

* 定义方式

  * 类型 `[]`：

  ```js
    let arr:string[]=['a','b','c']
    arr.push(8)// Argument of type '"8"' is not assignable to parameter of type 'string'.
  ```
  * 数组泛型：

  ```js
    let arr:Array<string>=['a','b','c']
  ```
  * interface

  ```js
    interface stringArray{
      [index:string]:string
    }
    let letterString:stringArray=['a','b','c']
  ```
  stringArray表示当索引是string类型时，值必须也是string类型
  我们一般使用interface表示类数组，类数组不是数组类型，比如 `arguments`

  ```js
  function sum() {
  let args: number[] = arguments;
  }
  // Type 'IArguments' is missing the following properties from type 'number[]': pop, push, concat, join, and 24 more.
  ```
  上例中，arguments 实际上是一个类数组，不能用普通的数组的方式来描述，而应该用接口：

  ```js
  function sum() {
  let args: {
      [index: number]: number;
      length: number;
      callee: Function;
  } = arguments;
  }
  ```
  在这个例子中，我们除了约束当索引的类型是数字时，值的类型必须是数字之外，也约束了它还有 length 和 callee 两个属性。

  事实上常用的类数组都有自己的接口定义，如 IArguments, NodeList, HTMLCollection 等：

  ```js
  function sum() {
  let args: IArguments = arguments;
  }
  ```
  其中 IArguments 是 TypeScript 中定义好了的类型，它实际上就是：

  ```js
  interface IArguments {
  [index: number]: any;
  length: number;
  callee: Function;
  }
  ```
