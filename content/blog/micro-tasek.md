---
date: 2023-02-27
title: 宏任务和微任务
---
## 微任务和宏任务

### 概念

* 事件循环中并非只维护着一个队列，事实上是有两个队列
  * 宏任务队列(macrotask queue) ： **ajax** 、 **setTimeout** 、 **setInterval** 、 **DOM监听** 、Ul Rendering等
  * 微任务队列(microtask queue)： **Promise的then回调** 、Mutation Observer API、queueMicrotask()等
  * 【 **注意：new Promise中的代码会直接执行的，只有Promise的then回调才会加入到微任务队列中** 】
* 那么事件循环对于两个队列的优先级是怎么样的呢?
  * main script中的代码优先执行(编写的顶层script代码)
  * 在执行任何一个宏任务之前(不是队列，是一个宏任务)，都会先查看微任务队列中是否有任务需要执行
    * 也就是宏任务执行之前，必须保证微任务队列是空的;
    * 如果不为空，那么就优先执行微任务队列中的任务(回调)

### 面试题一

画微任务、宏任务、执行上下文的图去分析

```js
console.log("script start");

setTimeout(function(){
console.log("setTimeout1");
newPromise(function(resolve){
resolve()
}).then(function(){
newPromise(function(resolve){
            resolve
}).then(function(){
console.log("then4");
})
console.log("then2");
})
})

newPromise(function(resolve){
console.log("promise1");
resolve()
}).then(function(){
console.log("then1");
})

setTimeout(function(){
console.log("setTimeout2");
})

console.log(2);

queueMicrotask(()=>{
console.log("queueMicrotask1");
})

newPromise(function(resolve){
resolve()
}).then(function(){
console.log("then3");
})

console.log("script end");
```

```js
script start
promise1
2
script end
then1
queueMicrotask1
then3
setTimeout1
then2
then4
setTimeout2
```

### 面试题二

```js
console.log("script start");

functionrequestData(url){
returnnewPromise((resolve)=>{
setTimeout(()=>{
console.log("setTimeout");
resolve(url)
},2000)
})
}

functiongetData(){
console.log("getData start");
requestData("why").then(res=>{
console.log("res:",res);
})
console.log("getData end");
}

getData()

console.log("script end");
```

```js
script start
getData start
getData end
script end
setTimeout
res:why
```

### 面试题三

解析 async 和 await 的执行顺序

在异步函数中，await后续的代码相当于在then中，等到promise.resolve有结果才执行，但出了async代码后不会等await，会立即执行

```js
console.log("script start");

functionrequestData(url){
console.log("requestData");
returnnewPromise((resolve)=>{
setTimeout(()=>{
console.log("setTimeout");
resolve(url)
},2000)
})
}

asyncfunctiongetData(){
// 1和2就是普通的执行函数，一起执行，执行完2后，3和4相当于一起加入到then（微任务）中，等待执行上下文执行完，在执行微任务
console.log("getData start");//1
const result =awaitrequestData("why")//2
console.log("res:",result);//3
console.log("getData end");//4
}

getData()

console.log("script end");
```

```js
script start
getData start
requestData
script end
setTimeout
res:why
getData end
```

### 面试题四

```js
asyncfunctionasync1(){
console.log("async1 start");
awaitasync2()
console.log("ascyn1 end");
}

asyncfunctionasync2(){
console.log("async2");
}

console.log("script start");

setTimeout(function(){
console.log("setTimeout");
})

async1()

newPromise(function(resolve){
console.log("promise1");
resolve()
}).then(function(){
console.log("promise2");
})

console.log("script end");
```

```js
script star
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```
