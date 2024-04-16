---
date: 2023-11-12
url:
aliases:
title: 响应式原理
---
## 响应式数据与副作用函数

副作用函数指的是会产生副作用的函数，如下面所示

```js
function effectFn1() {
  document.body.innerText = "reactive";
}
let a = 1;
function effectFn2() {
  a = 2;
}
```

effectFn1 会设置 body 的文本内容，但除它之外的其他函数也可以改变 body.innerText，所以 effectFn1 的执行直接或间接地影响了其他函数的执行，此时我们称 effectFn1 为副作用函数;

effectFn2 的执行改变了全局变量 a，也属于产生了副作用，所以 effectFn2 也称为副作用函数。

理解了什么是副作用函数，再来说说什么是响应式数据。假设在

一个副作用函数中读取了某个对象的属性：

```js
const obj = { text: "hello world" };
function effectFn() {
  // effect 函数的执行会读取 obj.text
  document.body.innerText = obj.text;
}
```

我们希望做到的是修改 obj.text，effectFn 自动重新执行，即 body.innerText 重新渲染

## 响应式数据的实现思路

如何才能让上文的 obj 成为一个响应式数据呢？我们不难发现

- 当副作用函数 effect 执行时，会触发字段 obj.text 的读取操作；
- 当修改 obj.text 的值时，会触发字段 obj.text 的设置操作

如果我们能拦截一个对象的读取和设置操作，事情就变得简单了，
当 `读取字段 obj.text` 时，我们可以把副作用函数 effect 存储到一个“桶”里：

<img  src="/img/notes/get.png"/>

接着，当 `设置 obj.text` 时，再把副作用函数 effect 从“桶”里
取出并执行即可

<img  src="/img/notes/set.png"/>

在Es2015之前，我们通过Object.defineProperty来拦截对象属性的读取和设置，这也是Vue2采用的api，而Vue3当中，我们使用代理对象 `Proxy`来实现

```js
// 存储副作用函数的桶
const bucket = new Set()

// 原始数据
const data = { text: 'hello world' }
// 对原始数据的代理
const obj = new Proxy(data, {
// 拦截读取操作
get(target, key) {
// 将副作用函数 effect 添加到存储副作用函数的桶中
bucket.add(effect)
// 返回属性值
return target[key]
},
// 拦截设置操作
set(target, key, newVal) {
// 设置属性值
target[key] = newVal
// 把副作用函数从桶里取出并执行
bucket.forEach(fn => fn())
// 返回 true 代表设置操作成功
return true
}
})
```

```js
// 副作用函数
function effect() {
document.body.innerText = obj.text
}
// 执行副作用函数，触发读取
effect()
// 1 秒后修改响应式数据
setTimeout(() => {
obj.text = 'hello vue3'
}, 1000)
```

上文的实现存在一些缺陷如：

* 通过名称effect获取副作用函数不合理，我们希望自己定义副作用函数名，或者传入匿名函数，这种硬编码机制需要优化

## 完备的响应式系统

为了解决副作用函数硬编码问题，我们需要提供一个用来注册副作用函数的机制

```js
//定义一个全局变量存储已注册的副作用函数（当前激活的副作用函数）
let activeEffect;
//定义一个注册副作用函数的函数
function registerEffectFn(fn) {
  // 当调用 registerEffectFn 注册副作用函数时，将副作用函数 fn 赋值给activeEffect
  activeEffect = fn;
  //执行副作用函数
  fn();
}
```

我们可以按照如下所示的方式使用 effect 函数

```js
registerEffectFn(function effect() {
  document.body.innerText = obj.date;
});
```

但如果我们再对这个系统稍加测试，例如在响应式数据 obj 上设置一个不存在的属性时：

```js
registerEffectFn(function effect() {
  console.log("effect run");//打印两次
  document.body.innerText= obj.date;
});

setTimeout(() => {
// 副作用函数中并没有读取 notExist 属性的值
obj.notExist = 'hello vue3'
}, 1000)
```

匿名副作用函数内部读取了字段 obj.text 的值，于是匿名副作用函数与字段 obj.text 之间会建立响应联系。接着，我们开启了一个定时器，一秒钟后为对象 obj 添加新的 notExist 属性。我们知道，在匿名副作用函数内并没有读取 obj.notExist 属性的值，所以理论上，字段 obj.notExist 并没有与副作用建立响应联系，因此，定时器内语句的执行不应该触发匿名副作用函数重新执行。但如果我们执行上述这段代码就会发现，定时器到时后，匿名副作用函数却重新执行了，这是不正确的。为了解决这个问题，我们需要重新设计“桶”的数据结构

那应该设计怎样的数据结构呢？在回答这个问题之前，我们需要先仔细观察下面的代码：

```js
registerEffectFn(function effect() {
  document.body.innerText = obj.date;
});
```

在这段代码中存在三个角色：

* 被操作（读取）的代理对象 obj；
* 被操作（读取）的字段名 date
* 使用 registerEffectFn 函数注册的副作用函数 effect

如果用 target 来表示一个代理对象所代理的原始对象，用 key来表示被操作的字段名，用 effect 来表示被注册的副作用函数，那么可以为这三个角色建立如下关系：

```js
 target
   └── key
        └── effect
```

我们可以使用如下图的数据结构：

<img src="/img/notes/weakmap.png"/>

#### 完整代码

```js
//定义一个全局变量存储已注册的副作用函数（当前激活的副作用函数）
let activeEffect;
//定义一个注册副作用函数的函数
function registerEffectFn(fn) {
  // 当调用 registerEffectFn 注册副作用函数时，将副作用函数 fn 赋值给activeEffect
  activeEffect = fn;
  //执行副作用函数
  fn();
}
//需要响应式的数据
const data = { date: "20240225" };
//封装响应式系统（vue3 ref）
//定义一个存储副作用函数的桶
const bucket = new WeakMap();
//代理data
const obj = new Proxy(data, {
  get(target, key) {
    //收集依赖
    track(target, key);
    //返回读取的值
    return target[key];
  },
  set(target, key, newVal) {
    //设置新值
    target[key] = newVal;
    //触发依赖
    trigger(target, key);
  },
});
//收集依赖
function track(target, key) {
  //如果未注册副作用函数
  if (!activeEffect) return;
  //根据target获取bucket中的depsMap(target=>map)
  let depsMap = bucket.get(target);
  if (!depsMap) {
    //如果没有depsMap，新建一个depsMap与bucket相关联
    bucket.set(target, (depsMap = new Map()));
  }
  //根据key获取depsMap中的depsSet(key=>set)
  let depsSet = depsMap.get(key);
  if (!depsSet) {
    //如果没有depsSet，新建一个depsSet与depsMap相关联
    depsMap.set(key, (depsSet = new Set()));
  }
  //将注册的副作用函数收集到set中
  depsSet.add(activeEffect);
}

function trigger(target, key) {
  //根据target获取bucket中的depsMap(target=>map)
  const depsMap = bucket.get(target);
  if (!depsMap) return;
  //根据key获取depsMap中的effects(depsSet(key=>set))
  const effects = depsMap.get(key);
  effects && effects.forEach((f) => f());
}

//示例
registerEffectFn(function effect() {
  console.log("effect run");
  document.body.innerHTML = obj.date;
});
setTimeout(() => {
  obj.date = "20240226";
}, 1000);
```

#### 使用WeakMap存储副作用函数的原因

WeakMap对key是弱引用，不影响垃圾回收器的工作。一旦key被垃圾回收器回收，那么对应的键和值就访问不到了。所以WeakMap一般用来存储那些只有当key所引用的对象存在(没有被回收)才有价值的数据，例如该场景，当target对象不存在任何引用，即用户不再需要它，垃圾回收器会回收它，而如果用Map代替WeakMap，即使target对象不存在任何引用，由于Map是强引用，target也不会被回收，可能导致内存溢出
