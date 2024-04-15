---
date: 2023-02-06
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
  * 方式二：**使用ts-node库**

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
  * `let/var/const 标识符: 数据类型 = 赋值;`【`let message: string = "hello world"`】
* 注意：ts中声明类型的string是小写的，和String是有区别的

### 变量的类型推导

* 在开发中，有时候为了方便起见我们并不会在声明每一个变量时都写上对应的数据类型，我们更希望可以通过TypeScript本身的特性帮肋我们推断出对应的变量类型
* 如果我们给message赋值为123，那他的类型就默认为number
* 这是因为在一个变量第一次赋值时，会根据后面的赋值内容的类型，来推断出变量的类型
* 声明一个标识符时，如果有直接进行赋值，会根据赋值的类型推导出标识符的类型注解，这个过程称之为类型推导
  * let进行类型推导，推导出来的通用类型
  * const进行类型推导，推导出来的字面量类型（后续专门讲解)

### js与ts的数据类型

js和ts大部分数据类型都是一样的，我们就不演示了，主要说明不一样的

number、boolean、string、Symbol、null、undefined等

#### Array类型

明确的指定 <数组> 的类型注解：

* string[] ：数组类型，并且数组中存放的字符串类型
  * 一般在我们开发过程中，数组中放的都是相同的类型，不要放不同的类型
  * ```ts
    let arr:string[]=["aaa","bbb","ccc"]
    arr.push("ccc")
    arr.push(123)//报错，类型不同
    ```
* 第二种写法：`Array<string>`，这是泛型的写法，后续学习

#### Object类型

* 明确指定
  * ```typescript
    let obj:{
        name:string,
        age:number
    }={
        name:'jl',
        age:18
    }
    ```
  * 注意：我们不建议直接使用object类型来作为对象的类型，因为设置了object类型我们就不能获取数据，也不能设置数据 `let obj:object ={...}`【不建议 】
* 使用type关键字声明，后续学习

#### any类型

* 在某些情况下，我们确实无法确定一个变量的类型，并且可能它会发生一些变化，这个时候我们可以使用any类型
* any类型表示不限制标识符的任意类型，并且可以在该标识符上面进行任意的操作

#### unknown类型

* unknown是TypeScript中比较特殊的一种类型，它用于描述类型不确定的变量。
  * 和any类型有点类似，但是unknown类型的值上做任何事情都是不合法的【any任何操作都是合法的】
  * ```ts
    let foo:any="aaa"
    foo =123// 可以
    console.log(foo.length)// 可以
    ```
  * ```ts
    let foo:unknown="aaa"
    foo =123// 可以
    console.log(foo.length)// 报错
    ```
* 那不允许操作有什么用呢？
  * 他要求我们必须要进行类型的校验（缩小），才能根据缩小之后的类型进行对应的操作
  * ```ts
    let foo:unknown="aaa"
    foo =123// 允许的
    // 类型缩小
    if(typeof foo ==='string'){
    console.log(foo.length)// 可以
    }
    ```

#### void类型

* void通常用来指定一个函数是没有返回值的，那么它的返回值就是void类型
  * ```ts
    functionsum(num1:number, num2:number):void{
    console.log(num1 + num2);
    }
    ```
* 如果返回值类型是void类型，那么我们也可以返回undefined
  * ```ts
    functionsum(num1:number, num2:number):void{
    console.log(num1 + num2);
    returnundefined
    }
    ```
* 应用场景：用来指定函数类型是void
  * ```ts
    typefooType=()=>void//这个表达式可以用来表示函数类型
    const foo:fooType=()=>{

    }
    ```
  * ```js
    // 举例：将所有的函数都延迟一秒执行
    functiondelayFn(fn){
    setTimeout(()=>{
    fn()
    },1000)
    }

    delayFn(123)//在这里我们传入123就不合适了


    // 我们指定fn的类型为一个函数类型
    functiondelayFn(fn:()=>void){}
    ```
* 关于定义函数的类型
  * 当**基于上下文的类型推导推导出来的返回类型**为void的时候，并不会强制函数一定不能返回内容。允许我们返回东西
  * ```js
    msg.forEach((item, index, arr)=>{
    console.log(item)
    return111//允许的，但是不建议，没有意义
    })
    ```

#### never类型

* 开发中很少定义never类型，在某些情况下进行类型推导会有never
* never表示永远不会发生值的类型，比如一个函数：
  * 如果一个函数中是一个死循环或者抛出一个异常，那么这个函数会返回东西吗?
  * 不会，那么写void类型或者其他类型作为返回值类型都不合适，我们就可以使用never类型

#### tuple类型

* tuple是元组类型，很多语言中也有这种数据类型，比如Python、Swift等
* 例子：当我们保存个人信息的时候：
  * ```ts
    // 不合适：数组中最好存放相同的数据类型，并且我们取出的数据不知道是什么类型
    let info1:any[]=['ygy',18,1.88]

    //使用对象类型，这也是我们使用最多的
    let info2 ={
        name:'ygy',
        age:18,
        heigth:1.88
    }

    //使用元组类型：元组中可以存放不同的数据类型，取出的数据具有明确的类型
    let info3:[string,number,number]=['ygy',18,1.88]
    ```
* 使用场景：在函数中使用较多，特别是函数的返回值
  * ```js
    // react中的useState

    functionuseState(initState:any):[ number,(newValue: number)=>void]{
    let stateValue = initState

    functionsetStateValue(newValue: any){
            stateValue = newValue
    }

    return[ stateValue, setStateValue ]

    }

    const[ num, setNum ]=useState(10)
    ```
* 那么tuple和数组有什么区别呢?
  * 首先， **数组中通常建议存放相同类型的元素，不同类型的元素是不推荐放在数组中** 。(可以放在对象或者元组中)
  * 其次，**元组中每个元素都有自己特性的类型，根据索引值获取到的值可以确定对应的类型**

### 参数的类型

在ts中定义一个函数时，要明确的指定参数的类型

```typescript
functionadd(num1:number, num2:number){
return num1 + num2
}
```

### 返回值类型

不指定也是ok的，它会自动进行类型推导

```ts
functionadd(num1:number, num2:number):number{
return num1 + num2
}
```

### 匿名函数的参数类型

匿名函数最好不要添加类型注解。因为函数执行的上下文可以帮助确定参数和返回的类型

### 对象类型的使用

先了解

对象类型和函数类型一起使用

```ts
typeposType={
    x:number,
    y:number
}

functionposition(obj : posType){
console.log(`x:${obj.x}`);
console.log(`y:${obj.y}`);
}

position({ x:30, y:40})
```

## TS语法细节

### 联合类型

* TypeScript的类型系统允许我们使用多种运算符，从现有类型中构建新类型
* 我们来使用第一种组合类型的方法：联合类型(Union Type)
  * 联合类型是由两个或者多个其他类型组成的类型
  * 表示可以是这些类型中的任何一个值
  * 联合类型中的每一个类型被称之为联合成员(union's members)
* 使用 `|` (或)
  * 基本使用：`let foo : number | string = '123'`
  * 两种/多种满足一个即可
* 一般我们在使用联合类型的时候，会进行缩小类型
  * ```ts
    let str:number|string='ygy'

    // str = 123

    // 直接使用会报错：number上不存在length属性
    // console.log(str.length);

    if(typeof str =="string"){
    console.log(str.length);
    }
    ```

### 类型别名

关键词：`type`

```ts
typepointType={
    x:number,
    y:number,
    z?:number,//可选
}

functionpoint(obj: pointType){
console.log(obj.x, obj.y, obj.z);
}
```

```js
type my = string | number
letstr: my =123
```

### 接口的声明

使用关键词 `interface`

* 类型别名是赋值的方式
  * 类似于 `const name = "ygy"`。之后我们在可以使用ygy，也可以使用name
  * `type Atype = number | string`。type也类似这样的
* 接口是声明的方式：
  * 类似于 `class Person{}`
  * `interface PointType{}`
  * ```ts
    interfacepointType{
        x:number,
        y:number,
        z?:number,
    }

    functionpoint(obj: pointType){
    console.log(obj.x, obj.y, obj.z);
    }
    ```

### interface和type的区别

* 类型别名和接口是非常相似的，在定义对象的时候，大部分时候两者都是可以使用的
* **接口几乎所有的特性都可以在type中使用**
* 区别一：type类型使用范围更广泛，interface只能用来声明对象
* 区别二：
  * 在声明对象的时候，type不允许两个名称相同的别名存在。
  * interface可以多次声明，并且两次声明的值都是有效的都需要满足
* 区别三：interface支持继承
  * ```ts
    interfaceperson{
        name:string,
        age:number
    }

    interfaceAextendsperson{
        height:number,
    }
    ```
* 区别四：interface可以被类实现【这里先知道就行】
* ...

**一般在开发中如果是非对象类型的定义使用type，如果是对象类型的声明那么使用interface，因为interface的功能更多一些**

### 交叉类型

* 使用 `&` (且)
  * 错误使用：`let foo : number & string = 'ygy'//这是没有意义的`
  * ```ts
    //正确使用：
    interfaceOne{
        name:string,
        age:number
    }

    interfaceTwo{
        name:string,
    coding:()=>void
    }

    let Three : One & Two

    let info: Three ={
        name:'why',
        age:18,
    coding:function(){
    console.log("111")
    }
    }
    ```
  * 两种/多种同时满足才行

### 类型断言as

* 有时候TypeScript无法获取具体的类型信息，这个我们需要使用类型断言（Type Assertions)。
  * 比如我们通过 `document.getElementByld`， TypeScript只知道该函数会返回 `HTMLElement`，但并不知道它具体的类型
  * ```ts
    // 获取Dom：<img class="img" />
    // 我们在使用标签选择的时候，是什么类型我们是比价确定的
    const myEl = ducument.querySelect("img")as HTMLImageElemnt

    // 但是开发中我们一般会设置class去获取class，这个时候ts的类型推断是 Element | null，我们不能获取具体的类型
    // 这个时候我们可以用类型断言
    const myEl = ducument.querySelect(".img")as HTMLImageElemnt
    myEl.src ='..'
    ```
* TypeScript只允许类型断言转换为更具体或者不太具体的类型(一般是any/unknown)版本，此规则可防止不可能的强制转换

### 非空类型断言

非空类型断言：其实就是告诉编译器 `info.firend`一定不为空，不需要帮我检测，**但这个是危险的** 。只有在确保我们这个 `info.friend`是有值的情况下才能使用

非空断言使用的是!，表示可以确定某个标识符是有值的，跳过ts在编译阶段对它的检测

`info.firend!.name = "jl"`

```ts
interfacePerson{
    name:string,
    age:number,
    friend?:{
        name:string
}
}

const info: Person ={
    name:"why",
    age:18
}

//访问属性的时候，可以使用可选链 ?.
console.log(info.friend?.name)

//当我们进行赋值的时候，不能使用可选链了
info.firend?.name ="jl"//错误的

//解决方法一：类型缩小
if(info.friend){
    info.firend.name ="jl"
}

//解决方法二：非空类型断言：其实就是告诉编译器info.firend一定不为空，不需要帮我检测，但这个是危险的
info.firend!.name ="jl"
```

### 字面量类型

```ts
// 1.基本使用
const name:'why'='why'
const age:18=18

// 2.但一般我们不会这样用，那是没有意义的，一般我们会将多个字面量类型联合在一起
typeDirection='left'|'right'|'up'|'down'
let d1: Direction ='left'//这个赋值只能是在四个当中的一个

// 3.例子：封装请求：
functionrequest(url:string, method:'post'|'get'){}

// 4.ts的小细节，关于这个请求的
typeMethodType='post'|'get'
functionrequest(url:string, method: MethodType){}

const info ={
    url:"xxx",
    method:'post'
}
request(info.url, info.method)//这个是错误的，因为info.method类型是string，由info推导出来的

// 解决方法一：类型断言
request(info.url, info.method as MethodType)

// 解决办法二：给info添加变量声明
const info:{
    url:string,
    method: MethodType
}={
    url:"xxx",
    method:'post'
}

// 解决方法三：强制变成自变量类型
const info ={
    url:"xxx",
    method:'post'
}asconst//这个时候我们的url变成自变量类型xxx，method变成自变量类型post
```

### 类型缩小

* 什么是类型缩小
  * 我们可以通过类似于 `typeof padding === “number”`的判断语句，来改变TypeScript的执行路径
  * 在给定的执行路径中，我们可以缩小比声明时更小的类型，这个过程称之为缩小(Narrowing )
  * 而我们编写的 `typeof padding === "number"`可以称之为类型保护(type guards)
* 常见的类型保护如下
  * typeof
  * instanceof：判断是不是某一个类的实例
    * ```ts
      // 传入时间打印
      functionprintDate( date:string| Date ){
      if( date instanceofDate){
      console.log(date);
      }
      }
      ```
  * in：用于判断某个对象是否具有带名称的属性，有返回true
    * ```ts
      interfaceISwim{
      swim:()=>void;
      }

      interfaceiRun{
      run:()=>void;
      }

      functionmove( animal: ISwim | iRun ){
      // 我们不能通过这样的方式去判断，因为animal可能没有ISim
      // if( animal.ISwim )

      if("swim"in animal ){
      console.log("swim");
      }else{
      console.log("run");
      }

      }
      ```
  * 平等缩小（`=== 、!==`）

## TS函数语法

### ts函数类型

#### 编写函数类型表达式，来表示函数类型

* 格式：`(参数列表) => 返回值`，**使用箭头**
* `const bar: () => void = () =>{}`
* ```ts
  // 0.声明式函数：const bar = (arg) =>{}
  const bar =(arg :number):number=>{
  return123
  }

  // 1.给函数添加上类型
  constbar:(参数列表)=> 返回类型 =(arg :number):number=>{
  return123
  }
  // 2.加上返回值类型
  constbar:(参数列表)=>number=(arg :number):number=>{
  return123
  }
  // 3.加上参数类型
  // 3.1 但是这样写是错的，必须先写形参的名字，在写类型
  constbar:(number)=>number=(arg :number):number=>{
  return123
  }
  // 3.2 正确写法
  constbar:(num1:number)=>number=(arg :number):number=>{
  return123
  }


  //起别名
  typebarType=(num1:number)=>number
  const bar1:barType=(arg:number)=>{
  return123
  }
  ```
* 小案例：
  * ```js
    // js代码
    functioncalc(calcFn){
    const num1 =10
    const num2 =20

    // 将进行什么计算交给传来的函数
    const res =calcFn(num1, num2)
    console.log(res)
    }

    functionadd(num1, num2){
    return num1 + num2
    }
    calc(add)
    ```
  * ```js
    // ts代码
    type calcType=(num1: number,num2: number)=> number
    functioncalc(calcFn: calcType){
    const num1 =10
    const num2 =20

    // 将进行什么计算交给传来的函数
    const res =calcFn(num1, num2)
    console.log(res)
    }

    functionadd(num1: number,num2: number){
    return num1 + num2
    }
    calc(add)
    ```
* 小细节：ts对于传入的函数类型的参数个数不进行检验【比如我们使用高阶函数forEach，我们可以就传一个参数】
  * ```js
    type calcType=(num1: number,num2: number)=> number
    functioncalc(calcFn: calcType){
    const num1 =10
    const num2 =20

    // 将进行什么计算交给传来的函数
    const res =calcFn(num1, num2)
    console.log(res)
    }

    // 下面都是允许的
    calc(function(num1, num2){
    return num1+num2
    })

    calc(function(num1){
    return num1
    })

    calc(function(){
    return1
    })
    ```

#### 函数调用签名

* 我们从对象的角度来看，当我们的函数赋值给一个对象的时候，这个对象只能表达这个函数，但是我们这个对象还可能有其他的属性
* 但是函数声明表达式并不能支持声明属性
* 语法：`(参数列表): 返回值类型`，**这里我们就是冒号了而不是箭头**
* ```ts
  //函数类型表达式：
  typebarType=(num1:number)=>number
  const bar: barType =(arg:number):number=>{
  return123
  }

  // 从对象的角度来看,对象还会有其他的属性
  //不被允许
  bar.name ='why'
  bar.age =18
  ```
* 在JavaScript中，函数除了可以被调用，自己也是可以有属性值的。然而前面讲到的函数类型表达式并不能支持声明属性。如果我们想描述一个带有属性的函数，我们可以在一个对象类型中写一个**调用签名**
* ```ts
  //函数调用签名，它是一个对象，所以我们type interface都可以用，但一般对象我们推荐使用interface
  interfacebarType{
      name:string,
      age:number
  // 函数是可以被调用的：函数调用签名
  // (参数列表): 返回值类型 
  (num1:number):number
  }

  const bar:barType=(arg:number)=>{
  return123
  }

  bar.name ='why'
  bar.age =18
  bar(123)
  ```
* 开发中
  * 如果只是描述函数类型本身（函数可以被调用），使用函数类型表达式。
  * 如果描述函数作为对象可以被调用，同时也是有其他属性时，使用函数调用签名

### 构造签名

js函数也可以使用new操作符进行调用，当被调用的时候，ts为认为这是一个构造函数，因为他们产生一个新对象

```ts
classPerson{}// 代表的是构造函数 

interfaceICPerson{
new(): Person  //构造签名，在函数调用签名前添加new
}

functionfactory(fn: ICPerson){
const f =newfn()
return f
}

faction(Person)
```

### 函数的可选参数

```ts
// 1.y是一个可选参数,可选参数的类型：number | undefined
// 2.可选参数需要写在最后
functionfoo(x:number, y?:number){}

foo(10)
foo(10,20)

// 2.当我们设置了可选参数的时候，我们不能直接对其操作，因为它可能是undefined，需要进行类型缩小
functionfoo(x:number, y?:number){
if(y!==undefined){
console.log(y+10)
}
}
```

### 参数的默认值

```ts
// 函数的参数可以有默认值
// 1.有默认值的情况下，参数的类型注解可以省略
// 2.有默认值的情况下，可以接受undefined的值
functionfoo(x:number, y:number=100){}

foo(10)
foo(10,undefined)// 与foo(10)一样的
foo(10,20)
```

### 剩余参数

```ts
functionfoo(...nums:string[]){}
```

### 函数的重载

* 在TypeScript中，如果我们编写了一个add函数，希望可以仅对字符串与字符串之间和数字与数字类型之间进行相加，应该如何编写呢?
* 我们可能会这样来编写，但是其实是错误的
  * ```ts
    // 运算符“+”不能应用于联合类型，我们不能将string和number类型在一起
    functionadd(a1:number|string, a2:number|string){
    return a1 + a2
    }
    ```
  * 这个时候我们可以使用函数的重载写法
* 在TypeScript中，我们可以去编写不同的重载签名(overload signatures)来表示函数可以以不同的方式进行调用
* 一般是编写两个或者以上的重载签名，再去编写一个通用的函数以及实现
  * ```ts
    // 1.先编写重载签名，后面没有{}
    functionadd(arg1:number, arg2:number):number
    functionadd(arg1:string, arg2:string):string

    // 2.编写通用的函数实现(在我们实现了重载签名之后，通用函数是不能被调用)
    functionadd(arg1:any, arg2:any):any{
    return arg1 + arg2
    }

    add(19,20)
    add("aa","bb")
    add(20,"aa")// 报错，不被允许，我们调用的并不是any类型的add函数
    ```

### 函数重载和联合类型的区别

* 在开发中，可以使用联合类型尽量使用联合类型，联合类型实现不了在用函数重载

```ts
// 函数重载
functiongetLength(arg:string):number
functiongetLength(arg:any[]):number
functiongetLength(arg){
return arg.length
}

getLength("aaaa")
getLength(['aa','bb','cc'])
```

```ts
// 联合类型
functiongetLength( arg:string|any[]):number{
return arg.length
}

getLength("aaaa")
getLength(['aa','bb','cc'])
```

```ts
//拓展：对象类型实现
functiongetLength( str :{ length:number}):number{
return str.length
}

getLength("aaaa")
getLength(['aa','bb','cc'])
```

## this类型

### 默认类型

* 在没有对ts进行特殊配置的情况下，this是any类型
* 当我们使用 `tsc init`生成配置文件的时候，`"noImplicitThis": true`，ts会根据上下文推导this，在不能正确的推导的时候就会报错，需要我们明确的指定this
  * ```ts
    const info ={
        name:"why",
        studying:functon(){
    // 默认是any类型
    console.log(this.name +"studying")
    }
    }

    info.studying()
    ```
  * ```ts
    // 配置  "noImplicitThis": true
    const info ={
        name:"why",
        studying:functon(){
    //不会报错，因为他可以根据上下文推导出来，this类型是info类型
    console.log(this.name +"studying")
    }
    }

    info.studying()

    functionfoo(){
    console.log(this)//报错，推导不出来
    }
    ```

### 指定this的类型

* 在开启 `nolmplicitThis:true`的情况下，我们必须指定this的类型。
* 如何指定呢?函数的第一个参数类型:
  * 函数的第一个参数我们可以根据该函数之后被调用的情况，用于声明this的类型（ **名词必须叫this** )
  * 在后续调用函数传入参数时，从第二个参数开始传递的，this参数会在编译后被抹除
  * ```ts
    functionfoo(this:{name:string,age:number},other:any){
    console.log(this)
    console.log(other)
    }

    foo.call({name:'aa',age:19},{other:"hhh"})
    ```

### this相关的内置工具

* Typescript提供了一些工具类型来辅助进行常见的类型转换，这些类型全局可用。
* ThisParameterType
  * 用于提取一个函数类型Type的this (opens new window)参数类型
  * 如果这个函数类型没有this参数返回unknown
* OmitThisParameter:
  * 用于移除一个函数类型Type的this参数类型，并且返回当前的函数类型

```ts
functionfoo(this:{name:string,age:number}, other:any){
console.log(this)
console.log(other)
}

// 获取函数的类型
typefooType=typeof foo

// 1.获取函数中的this类型：使用ThisParameterType方法（采用泛型方式）
typefooThisType= ThisParameterType<fooType>

// 2.删除this参数类型，剩余的函数的类型返回
typePureThisType= OmitThisParameter<fooType>
```
