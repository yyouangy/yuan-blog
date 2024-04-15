---
date: 2023-02-20
url: 
aliases:
title: TypeScript高级
---
## TS面向对象

### 类的基本使用

* 我们来定义一个Person类：使用class关键字来定义一个类
* 我们可以声明类的属性:在类的内部声明类的属性以及对应的类型
  * 如果类型没有声明，那么它们默认是any的
  * 我们也可以给属性设置初始化值
  * 初始化我们可以在声明成员属性的时候初始化，也可以在constructor中初始化
  * 在默认的 `strictPropertylnitialization`模式下面我们的属性是必须初始化的，如果没有初始化，那么编译时就会报错。如果我们在 `strictPropertyInitialization`模式下确实不希望给属性初始化，可以使用 `name!: string`语法（后面了解）
* 类可以有自己的构造函数constructor，当我们通过new关键字创建一个实例时，构造函数会被调用;
  * 构造函数不需要返回任何值，默认返回当前创建出来的实例

```ts
classPerson{
// 在ts中，我们必须要先声明成员属性（name、age就是成员属性）
    name:string
// 也可以添加初始化值
    age:number=0

//也可以在constructor中初始化
constructor(name:string, age:number){
this.name = name
this.age = age
}
}

const p1 =newPerson("xjn",18)
```

### 类的成员修饰符

* 在TypeScript中，类的属性和方法支持三种修饰符: public、private、protected
  * public修饰的是在任何地方可见、公有的属性或方法，默认编写的属性就是public的
  * private修饰的是仅在同一类中可见、私有的属性或方法
  * protected修饰的是仅在类自身及子类（继承）中可见、受保护的属性或方法

```ts
classPerson{
    name:string
private age:number

constructor(name:string, age:number){
this.name = name
this.age = age
}

privateeating(){
console.log("吃饭");
}
}

const p1 =newPerson("xjn",18)
p1.eating()//报错，私有属性
```

### 类的readonly

只读属性不能进行写入操作

```ts
classPerson{
readonly name:string
    age:number

constructor(name:string, age:number){
this.name = name
this.age = age
}

privateeating(){
console.log("吃饭");
}
}

const p1 =newPerson("xjn",18)
p1.name ='hhh'//报错，name只读
```

### getters/setters

* 在前面一些私有属性我们是不能直接访问的，或者某些属性我们想要监听它的获取(getter)和设置(setter)的过程，这个时候我可以使用存取器。

```ts
classPerson{
// 约定：私有属性名字前面加上 _ 
private _name:string=''
    age:number=0

constructor(name:string){
this.name = name
}


setname(newValue:string){
this._name = newValue
}

getname(){
returnthis._name
}
}

const p1 =newPerson("xjn")
// 调用的其实是set name方法，但是使用get、set不需要类似于函数调用一样写括号
p1.name ="hhh"
// 调用的是get name方法
console.log(p1.name);
```

### 参数属性

* TypeScript提供了特殊的语法，可以把一个构造函数参数转成一个同名同值的类属性。
  * 这些就被称为参数属性(parameter properties) ;
  * 你可以通过在构造函数参数前 **添加一个可见性修饰符public private protected 或者readonly 来创建参数属性** ，最后这些类属性字段也会得到这些修饰符

```ts
classPerson{
    name:string
    age:number

constructor(name:string, age:number){
this.name = name
this.age = age
}
}

//算是语法糖写法：
classPerson{
constructor(private name:string,private age:number){
this.name = name
this.age = age
}
}

const p1 =newPerson('xjn',18)
console.log(p1.name)
```

### 抽象类abstract

* 我们知道，继承是多态使用的前提。
  * 所以在定义很多通用的调用接口时,我们通常会让调用者传入父类，通过多态来实现更加灵活的调用方式。
  * 但是，父类本身可能并不需要对某些方法进行具体的实现，所以父类中定义的方法，我们可以定义为抽象方法。
* 抽象类特点：
  * 抽象类是不能被实例的话（也就是不能通过new创建)
  * 抽象类可以包含抽象方法，也可以包含有实现体的方法
  * 有抽象方法的类，必须是一个抽象类
  * 抽象方法必须被子类实现，否则该类必须是一个抽象类

```ts
// 2.但是抽象方法必须实现在抽象类中
abstractclassnumAbs{
// 1.定义方法名称，但是父类并不实现，交给子类来实习各自不同的方法
// 我的称之为抽象方法：关键字abstract
abstractgetNum():any;

// 5.抽象类中也可以有普通的方法
abc(){}
}

classoneextendsnumAbs{
// 3.当子类继承抽象类的时候，其父类定义的抽象方法必须实现
getNum(){
return'one';
}
}

classtwoextendsnumAbs{
getNum(){
return'two';
}
}

functionaddNum(num: numAbs){
console.log(num.getNum());

}

addNum(newone())
addNum(newtwo())

// 4.同时我们的抽象类是不能实例化的
addNum(newnum())//报错
```

### 类的类型

* 类的本身也可以作为一种数据类型的
* 类的作用
  * 可以创建类对应的实例对象（new）
  * 类本身可以作为这个实例的类型
  * 类也可以当做一个有构造签名的函数

```ts
classPerson{}

const name:string='aaa'
// 1.可以创建类对应的实例对象，也可以作为类型
const p : Person =newPerson()

// 2. 类也可以当做一个有构造签名的函数
functionfactory( ctor:new()=>void){}
factory(Person)
```

### 对象类型的修饰符

* 属性?：可选的属性
* readonly

```ts
typePerson1{
    name?:string,
readonly age:number
}

interfacePerson2{
    name?:string,
readonly age:number
}
```

### 索引签名

* 什么是索引签名呢?
  * 有的时候，你不能提前知道一个类型里的所有属性的名字，但是你知道这些值的特征
  * 这种情况，你就可以用一个索引签名(index signature)来描述可能的值的类型
  * 索引签名：可以通过字符串索引，去获取一个值，也是一个字符串
* **索引签名的属性类型必须是string或者是number（不能是联合类型）** 【`[index: string/number] : string`】

```ts
interfaceIIndexType{
// 返回值类型的目的是告知通过索引去获取到的值是什么类型
[xxx]: yyy

// 类型只能是string和number其中的一个，不允许写联合类型
[ index: 类型 ]:any
[ index:number]:string
}
// 符合要求，我们可以通过 name[0]等去取值,且取到的string类型
const name: IIndexType =['a','b','c']
```

```ts
interfaceIIndexType{
[ index:string]:any
}
// 也是没有问题的，因为这两个本质上一个意思， name[0] => name['0']
const name: IIndexType =['a','b','c']
```

```ts
interfaceIIndexType{
[ index:string]:string
}
// 报错，报错原型就是严格的字面量赋值
// name是一个数组类型，我们确实index为string，他还有数组的一些方法比如forEach
const name: IIndexType =['a','b','c']
// 但是他的返回值不是sting，是函数
name.forEach => name['forEach']
```

* 解决方法
* 我们让其成为不新鲜的是不起作用的
* 设置any
* 设置联合类型

```ts
typea={
[index:string]:string,
    heigth:number,//也报错：因为我们设置了 签名为string，需要满足上面的要求解决办法就是 [index: string] : string |number
}

let b: a ={
    name:'xjn',
    age:18,//报错，因为我们设置了 签名为string
}

// 获取
console.log(b["name"],b.name);
```

* 当我们想写两个类型的时候，必须要分开写，不允许写在一起

```ts
// 这样我们通过name[0] / name['forEach'] 我们可以明确知道我们取什么类型
interfaceIIndexType{
[index:number]:number,
[ other:string]:any
}
```

* 数字类型索引的类型，必须是字符串类型索引的类型的子类型

```ts
interfaceIIndexType{
[index:number]:number,//上面的类似必须是其子类型
[ other:string]:number|string
}
```

* 如果索引签名中有定义其他属性，·其他属性返回的类型,﹑必须符合string类型返回的属性

```ts
interfaceIIndexType{
[index:number]:number,
[ other:string]:number|string

    aaa:string
    bbb: blooean//不被允许的，与上述定义的other矛盾了
}
```

### 接口继承与实现

作用：

* 减少代码的重复编写

```ts
interfaceperson{
    name:string,
    age:number
}

interfaceAextendsperson{
    height:number,
}
```

* 接口被类实现

```ts
interfacePersonA{
    name:string
    age:number
runing:()=>void
}

interfacePersonB{
    height:number,
playing:()=>void
}

classOneimplementsPersonA, PersonB{
    name:string
    age:number
    height:number
runing(){

}

playing(){

}
}
```

### 严格的字面量赋值检测

```ts
// 奇怪现象：

// 这个会报错：
interfacePerson{
    name:string,
    age:number
}

const a: Person ={
    name:'jl',
    age:18,

    height:1.88
}
```

```ts
// 但是当当我们将添加height先定义在赋值的时候：不报错
interfacePerson{
    name:string,
    age:number
}

let obj ={
    name:'jl',
    age:18,
    height:1.88
}

const b: Person = obj
```

**解释现象：**

* 第一次创建的对象字面量，称之为fresh（新鲜的）
* 对于新鲜的字面量，会进行严格的类型检测，必须完全满足类型的要求（不能有多余的属性）
* 当类型断言或对象字面量的类型扩大（比如赋值）时，新鲜度会消失，就不会报错

### ts枚举类型

* 枚举类型是为数不多的TypeScript特性有的特性之一：
  * 枚举其实就是将一组可能出现的值，一个个列举出来，定义在一个类型中，这个类型就是枚举类型
  * 枚举允许开发者定义一组命名常量，常量可以是数字、字符串类型

```ts
// 定义类是 class，定义函数是function，定义枚举类型是enum

// 定义枚举类型：枚举类型的作用就是将所有可能出现的值都枚举出来
enum Direction{
UP,
DOWN,
LEFT,
RIGHT
}

const d1: Direction = Direction.UP
```

* 枚举类型默认是有值的，但我们不写的时候，默认从0开始递增，我们也可以手动设置初始化值

```ts
enum Direction{
UP=100,
DOWN,//默认为101
LEFT="LEFT",//定义了LEFT初始化值，RIGHT也必须要定义初始化值
RIGHT="RIGHT"
}
```

## TS泛型编程

### 泛型基本使用

 **类型变量，** 他作用于类型，而不是值

```ts
// 更加通用
functionfoo<Type>(arg: Type): Type{
return arg;
}
```

我们可以通过两种方式来调用它

* 方式一：通过<类型>的方式将类型传递给函数
* 方式二：通过类型推导，自动推到出我们传入变量的类型
  * 在这里会推导出它们是字面量类型的，因为字面量类型对于我们的函数也是适用的
* **在开发中，如果他可以正确的推导我们就简写，无法正确的推导我们就手动指定**

```ts
// 方式一：完整写法
const a =foo<number>(123)

// 方式二：自动进行类型推导
const a =foo(123)
const b =foo("123")
const c =foo<string[]>(["123"])
```

* 我们也可以指定多个类型：`function foo<T, V>(arg: T, other: V){}`
* 开发中我们常用：T(type)、V(value)、K(key)、E(element)来定义泛型的类型

```js
// 练习
function useState<T>(initState:T):[T,(newState:T)=>void]{
let stateValue = initState

functionsetStateValue(newState:T){
        stateValue = newState
}


return[stateValue, setStateValue]
}

const[ count, setCount ]=useState(10)
```

### 泛型接口

在我们定义接口的时候也可以用泛型的

```ts
// 以前：
interfaceuser{
    name:string,
    age:number,
    height:string
}

// 但是现在当我们不确定类型的时候，我们可以使用泛型
interfaceuser<Type>{
    name:Type,
    age:number,
    height:Type
}

// 但是我们在使用泛型接口的时候需要指定其类型，因为ts无法正确的推导其类型
const a: user<string>={
    name:"why",
    age:18,
    height:"1.88"
}

// 同时我们还可以设置默认值：设置了默认值之后就可以不指定其类型
interfaceuser<Type =string>{}
```

### 泛型类

```ts
classPoint<Type>{
    x: Type
    y: Type

constructor(x:Type, y:Type){
this.x = x
this.y = y
}
}

const a =newPoint(1,2)
const b =newPoint<string>('1','2')
```

### 泛型约束的基本使用

* 有时候我们希望传入的类型有某些共性，但是这些共性可能不是在同一种类型中
  * 比如string和array都是有length的，或者某些对象也是会有length属性的
  * 那么只要是拥有length的属性都可以作为我们的参数类型，那么应该如何操作呢

```ts
// 以前
functiongetLength(arg :string|string[]){
console.log(arg.length);

}

getLength('123')
getLength(['a','b','c'])
getLength({ length:10})//当我们想传这样的数据的时候就会报错


// 解决办法：传参类型改为有length
functiongetLength(arg :{ length:number})
```

```ts
// 上述案例中，确实我们也暂时用不到泛型呀，我们可以改一下我们的案例，还是传入有length属性的参数，返回这个参数
functiongetLength(arg :{ length:number}){
return arg
}

// 这个时候我们接收的info所有的类型全部都丢失了
const info1 =getLength('123')
const info2 =getLength(['a','b','c'])
const info3 =getLength({ length:10})

// 我们可以修改
functiongetLength<T>(arg :T):T{
return arg
}

// 但是这样又有一个问题，传入的参数类型缺少了约束，我们的要求是传入getLength的参数，必须要有length，我们现在直接使用泛型，是没有越是的
const info4 =getLength(123)// 也不会报错，但是我们希望他会报错提示我们
```

```ts
// 添加约束，我们可以使用关键词extends
// 一般我们会将{ length: number }抽出去用interface/type
functiongetLength<Textends{ length:number}>(arg :T):T{
return arg
}

const info1 =getLength('123')
const info2 =getLength(['a','b','c'])
const info3 =getLength({ length:10})

const info4 =getLength(123)// 这个时候他就会报错了
```

### 泛型参数的使用约束

先了解关键词：`keyof`

```ts
interfaceuser{
    name:string,
    age:number
}

typeOne=keyof user   // One就是"name"|"age"的联合类型
```

* 在泛型约束中使用类型参数
  * 你可以声明一个类型参数，这个类型参数被其他类型参数约束
* 举个栗子:我们希望获取一个对象给定属性名的值
  * 我们需要确保我们不会获取obj上不存在的属性
  * 所以我们在两个类型之间建立一个约束;

```ts
// 原先
functiongetObj(obj, key){
return obj[key]
}

const info ={
    name:"jl",
    age:18
}

const a =getObj(info,'name')
```

```ts
// 我们在使用改方法的时候，我们需要限制一个条件：传入的key必须是我们obj里面有的key
// 所以我们需要添加约束：使用keyof
functiongetObj<O,KextendskeyofO>(obj:O, key:K){
return obj[key]
}

const info ={
    name:"jl",
    age:18
}

const a =getObj(info,'name')
const b =getObj(info,'address')// 报错
```

## 拓展知识

### TS类型检测 - 鸭子类型

我们先来看下述的代码

```ts
classPerson{
constructor(public name:string,public age:number){

}
}

functionprintPerson(p: Person){
console.log(p.name, p.age);
}
printPerson(newPerson('jl',18))// 这是没有问题的
printPerson({ name:'hh', age:20})//很奇怪，也不会报错没

// 我们再创建一个类
classDog{
constructor(public name:string,public age:number){

}
}

printPerson(newDog('hsq',2))// 更奇怪了。printPerson明明要求我们传入一个Person类型，但我们传入Dog类型却没有报错
```

这是因为：

* TypeScript对于类型检测的时候使用的鸭子类型
* 鸭子类型：如果一只鸟,走起来像鸭子,·游起来像鸭子·看起来像鸭子。那么你视以认为它就是一只鸭子
* 鸭子类型：只关心属性和行为,不关心你具体是不是对应的类型

### 类型查找

我们这里先给大家介绍另外的一种typescript文件: `.d.ts文件`

我们之前编写的typescript文件都是.ts 文件，这些文件最终会输出.js文件，也是我们通常编写代码的地方;

还有另外一种文件.d.ts 文件，它是用来做类型的声明(declare)，称之为 **类型声明** ( Type Declaration)或者 **类型定义** (Type Definition)文件。

它仅仅用来做类型检测，告知typescript我们有哪些类型;

### TS模块化

#### 概念

* TypeScript中最主要使用的模块化方案就是**ES Module**
* 在前面我们已经学习过各种各样模块化方案以及对应的细节，这里我们主要学习TypeScript中一些比较特别的细节。

#### 非模块

* 我们需要先理解 TypeScript认为什么是一个模块。
  * JavaScript规范声明任何没有export 的JavaScript 文件都应该被认为是一个脚本，而非一个模块。
  * 在一个脚本文件中，变量和类型会被声明在共享的全局作用域，将多个输入文件合并成一个输出文件，或者在HTML使用 多个 `<script>`标签加载这些文件。
* 如果你有一个文件，现在没有任何import或者export，但是你希望它被作为模块处理，添加这行代码：`export {}`
* 这会把文件改成一个没有导出任何内容的模块，这个语法可以生效，无论你的模块目标是什么

#### 内置类型导入

* 如果你导入的东西是一个类型（type/interface），那么推荐你在导入的时候加上type关键字，表明被导入的是一个类型
  * ```ts
    import{typeIFoo,typeIPerson}from'./utils'

    // 这样写也是对的
    importtype{  IFoo, IPerson }from'./utils'
    ```
* 这些可以让一个非TypeScript编译器比如Babel、swc或者esbuild知道什么样的导入可以被安全移除。

#### declare 声明模块

我们也可以声明模块，比如lodash模块默认不能使用的情况，可以自己来声明这个模块【声明模块都是编写在.d.ts文件中的】

```ts
declaremodule"loadsh"{
    expore functionjoin(arg:any[]): ang;
}
```

声明模块的语法: `declare module '模块名' {}`。在声明模块的内部，我们可以通过export导出对应库的类、函数等;

**声明文件模块**

在ts中我们使用图片是需要导入的，但是默认ts并不识别图片格式，我们需要声明文件模块

```ts
declaremodule"*.png"
declaremodule"*.jpg"
...
```

**声明命名空间**

```ts
// 我们在引入外部链接的时候，声明模块不合适，因为他就不是模块，我们声明命名空间:用jquery举例
declarenamespace ${
exportfunctionajax();
}
```

### 外部定义类型声明

* 外部类型声明通常是我们使用一些库（比如第三方库）时，需要的一些类型声明。
* 这些库通常有两种类型声明方式:
  * 方式一:在自己库中进行类型声明(编写.d.ts文件)，比如axios
  * 方式二:通过社区的一个公有库 `DefinitelyTyped`存放类型声明文件
    * github地址：`https://github.com/DefinitelyTyped/DefinitelyTyped`
    * 查找安装地址：``
    * 比如我们安装react的类型声明：`npm i @types/react --save-dev`
  * 方式三：自己定义声明文件

### 认识tsconfig.json文件

* 什么是tsconfig.json文件呢?(官方的解释)
  * 当目录中出现了tsconfig.json文件，则说明该目录是TypeScript项目的根目录
  * tsconfig.json文件指定了编译项目所需的根目录下的文件以及编译选项。
* 官方的解释有点“官方”，直接看我的解释。
* tsconfig.json文件有两个作用:
  * 作用一(主要的作用)︰让TypeScript Compiler在编译的时候，知道如何去编译TypeScript代码和进行类型检测
    * 比如是否允许不明确的this选项，是否允许隐式的any类型;
    * 将TypeScript代码编译成什么版本的JavaScript代码;
  * 作用二：让编辑器（比如VSCode）可以按照正确的方式识别TypeScript代码
    * 对于哪些语法进行提示、类型错误检测等等;
* JavaScrip项目可以使用jsconfig,json文件，它的作用与tsconfig,json 基本相同，只是默认启用了一些JavaScript相关编译选项

### TS内置工具

Typescript提供了一些工具类型来辅助进行常见的类型转换，这些类型全局可用。

* `ThisParameterType<Type>`
  * 用于提取一个函数类型Type的this (opens new window)参数类型
  * 如果这个函数类型没有this参数返回unknown
* `OmitThisParameter<Type>`：用于移除一个函数类型Type的this参数类型，并且返回当前的函数类型

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

* `Partial<Type>`：用于构造一个Type下面的所有属性都设置为可选属性
* `Required<Type>`：用于构造一个Type下面的所有属性都设置为必选属性
* `ReadOnly<Type>`：用于构造一个Type下面的所有属性都设置为只读属性

```ts
interfaceIPerson{
    name: sring,
    age:number,
    height?:string
}

// 将IPerson所有属性都变为可选的
typeIP1= Partial<IPerson>

// 将IPerson所有属性都变为必选的
typeIP2= Required<IPerson>

// 将IPerson所有属性都变为只读的
typeIP2= ReadOnly<IPerson>
```

* `Record<Keys, Type>`：用于创建一个新的对象类型，它所有的key是Keys类型，它所有的value是Type类型。
* `Pick<Type, Keys>`：用于构造一个类型，它是从Type类型里面挑了一些属性Keys
* `Omit<Type, Keys>`：用于构造一个类型，它是从Type类型里面过滤了一些属性Keys

```ts
interfaceIPerson{
    name: sring,
    age:number,
    height?:string
}

// 我只想选到 "name" 和 "age"
typeIP1= Pick<IPerson,"name"|"age">

// 我想过滤到 "height"
typeIP2= Pick<IPerson,"height">
```

* `Exclude<UnionType, ExcludeMembers>`：用于构造一个类型，它是从UnionType联合类型里面排除了所有可以赋给ExcludedMembers的类型。
* `Extract<Type, Union>`：用于构造一个类型，它是从UnionType联合类型里面提取类型。

```ts
typep1="name"|"age"|"height"

// 去除height
typep2= Exclude<p1,"height">

// 提取height
typep3= Extract<p1,"height">
```

* `NonNullable<Type>`：用于构造一个类型，这个类型从Type中排除了所有的null、undefined的类型。
* `ReturnType<Type>`：用于构造一个含有Type函数的返回值的类型。
* `InstanceType<Type>`：用于构造一个由所有Type的构造函数的实例类型组成的类型。

## 补充

### 对axios进行封装

```ts
// config index.ts
exportconstBASE_URL='http://codercba.com:8000'
exportconstTIMEOUT=10000
```

```ts
// modules index.ts
import{ jlReq1 }from"..";

jlReq1.request({
    url:'/home/multidata'
}).then(res =>{
console.log(res.data);
})
```

```ts
// request index.ts
import axios from'axios'
importtype{ AxiosInstance }from'axios'

import{ jlRequestConfig }from'./type'


classjlRequest{
    instance: AxiosInstance

// 创建axios实例
constructor(config: jlRequestConfig){
this.instance = axios.create(config)

// 请求拦截器
this.instance.interceptors.request.use((config)=>{
console.log("全局请求成功拦截，这里可以开启loading、header携带token等");

return config
},(error)=>{
console.log(error);
})

// 响应拦截器
this.instance.interceptors.response.use((res)=>{
console.log("全局响应成功拦截，这里可以去掉loading");

return res
},(error)=>{
console.log(error);
})

// 针对特定的实例添加拦截器
this.instance.interceptors.request.use(
            config.interceptors?.requestSuccessFn,
            config.interceptors?.requestFailureFn
)

this.instance.interceptors.response.use(
            config.interceptors?.responseSuccessFn,
            config.interceptors?.responseFailureFn
)
}

// 创建网络请求的方法
request(config: jlRequestConfig){

// 可以设置单次请求的成功拦截
// if(config.interceptors?.requestSuccessFn){
//     config = config.interceptors.requestSuccessFn(config)
// }

returnthis.instance.request(config)
}

get(config: jlRequestConfig){
returnthis.request({...config, method:'GET'})
}

post(config: jlRequestConfig){
returnthis.request({...config, method:'POST'})
}

}

exportdefault jlRequest
```

```ts
// request type.ts
importtype{ AxiosRequestConfig, AxiosResponse, InternalAxiosRequestConfig }from'axios'

// 在这里配置拦截器的类型，这样我们就可以动态的设置哪个请求有对应的拦截器
exportinterfacejlRequestConfigextendsAxiosRequestConfig{
// 可选
    interceptors?:{
        requestSuccessFn?:(config: InternalAxiosRequestConfig)=> InternalAxiosRequestConfig,
// requestSuccessFn?: (config: AxiosRequestConfig) => AxiosRequestConfig,
        requestFailureFn?:(error:any)=>any,
        responseSuccessFn?:(res: AxiosResponse)=> AxiosResponse,
        responseFailureFn?:(error :any)=>any,
}
}
```

```ts
// index.ts
import{BASE_URL,TIMEOUT}from"./config";
import jlRequest from"./request";

const jlReq =newjlRequest({
    baseURL:BASE_URL,
    timeout:TIMEOUT
})

const jlReq1 =newjlRequest({
    baseURL:BASE_URL,
    timeout:TIMEOUT,
// 当我们这个接口需要额外的拦截器的时候，我们就可以配置
    interceptors:{
requestSuccessFn(config){
console.log('jlReq1的请求拦截');

return config
},
requestFailureFn(error){
return error
},
responseSuccessFn(res){
return res
},
responseFailureFn(error){
return error
},
}
})

export{ jlReq, jlReq1 }
```

### 小知识

* 定义类是 class，定义函数是function，定义枚举类型是enum
* 当我们使用第三方库的时候他的返回值类型并没有导出，当时我们又想使用
  ```ts
  import{ login }from'.'

  let options = Parameters<typeof login>[0];// Parameters<typeof login>可以拿到所有的类型，我们可以去第0个

  let resp: ReturnType<typeof login>|null
  ```
