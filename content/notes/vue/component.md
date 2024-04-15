---
date: 2023-11-02
url: 
aliases:
title: 探索组件本质
---
> 当我们使用 `Vue` 或 `React`时，往往会将页面拆分成各种组件，通过拼装组件来形成页面和应用，就像搭积木一样，那么我们来思考一个问题：组件的产出是什么？

## 组件的产出

### 模板引擎

比起组件，“模板引擎”的概念在以前要更加流行。

我们可以使用 `lodash.template` 函数来回忆一下当年是如何用模板开发一个页面的：

```js
import { template } from 'lodash'

const compiler = template('<h1><%= title %></h1>')
const html = compiler({ title: 'My Component' })

document.getElementById('app').innerHTML = html
```

模板引擎的概念是：字符串+数据=>html

`lodash.template` 函数虽然称不上是“引擎”，但足以说明问题。

我们将模板字符串传递给 `template` 函数，该函数返回一个编译器 `compiler`，只要把数据传入 `compiler` 函数，便能得到最终想要渲染的内容。

当数据发生变化时，我们需要使用新的数据重新编译模板：

```js
const newHtml = compiler({ title: 'New Component' })
```

如果把上面的逻辑封装成一个函数，那么一个组件就诞生了：

```js
const MyComponent = props => {
  const compiler = MyComponent.cache || (MyComponent.cache = template('<h1><%= title %></h1>'))
  return compiler(props)
}
MyComponent.cache = null
```

我们可以这样使用它：

```js
document.getElementById('app').innerHTML = MyComponent({ title: 'MyComponent' })
```

### Virtual Dom

`MyComponent` 组件也许会带给你这样的感觉： **一个组件就是一个函数，给我什么样的数据，我就渲染对应的 html 内容** 。

这个概念，与我们如今谈论的 `Vue` 或 `React` 并没有什么不同。所以，这就是**组件的本质** 。

组件的本质虽然没变，但组件的产出却改变了。在模板引擎的年代，组件的产出是 `html` 字符串

而如今的 `Vue` 或 `React`，它们的组件所产出的内容并不是 `html` 字符串，而是大家所熟知的 `Virtual DOM`。

对于Vue来说，一个组件最核心的就是render函数，data，computed，props，等都是为render函数提供数据来源的，render函数本可以直接产出html字符串，但是它却产出了virtual dom，借助 `snabbdom` 的 API 我们可以很容易地用代码描述这个公式：

```js
import {h} from "snabbdom";
//组件的产出时vDom，h函数用来创建vDom
const myComponent=props=>return h('h1',props.title)
```

`Virtual DOM` 终究要渲染真实 DOM，这个过程就可以理解为模板引擎年代的完全替换 `html`，只不过它采用的不是完全替换，我们通常把这个过程叫做 `patch`，同样可以借助 `snabbdom` 的 API 轻松地实现：

```js
import { h, init } from 'snabbdom'
// init 方法用来创建 patch 函数
const patch = init([])

const MyComponent = props => {
  return h('h1', props.title)
}

// 组件的产出是 VNode
const prevVnode = MyComponent({ title: 'prev' })
// 将 VNode 渲染成真实 DOM
patch(document.getElementById('app'), prevVnode)
```

当数据变更时，组件会产出新的 `VNode`，我们只需再次调用 `patch` 函数即可：

```js
// 数据变更，产出新的 VNode
const nextVnode = MyComponent({ title: 'next' })
// 通过对比新旧 VNode，高效地渲染真实 DOM
patch(prevVnode, nextVnode)
```

### 原因

以上说明了组件的产出=Virtual Dom，那么为什么组件要从直接产出html改成产出vDom呢？

* 将真实dom抽象成vDom，有效减少直接操作dom的次数，提高程序的性能
* 方便实现跨平台，同一个vnode节点可以渲染为不同平台对应的内容，比如：渲染在浏览器是 dom 元素节点，渲染在 Native( iOS、Android) 变为对应的控件、可以实现 `SSR(Nuxt.js/Next.js)`、原生应用(`Weex/React Native`)、小程序(`mpvue/uni-app`)等 、渲染到 WebGL 中等等。Vue3 中允许开发者基于 VNode 实现自定义渲染器（renderer），以便于针对不同平台进行渲染

## 组件的vnode如何表示

vnode是真实dom的描述，比如我们可以用下面一个对象表示真实dom

```js
const vnode={
	tag:'div'
			}
```

想要把vnode渲染成一个真实dom，我们还需要一个渲染器(Renderer)

```js
function render(vnode, container) {}
```

渲染器接收两个参数，分别是将要渲染的 `vnode` 和 元素挂载点(真实 DOM 被渲染的位置)。

为了渲染如上的 `div` 标签，我们可以这样调用 `render` 函数：

```js
// 把 vNode 渲染到 id 为 app 的元素下
render(vnode, document.getElementById('app'))
```

render函数的实现也很简单，render函数中调用了mountEl方法，mountEl中根据vnode的tag创建了真实dom并挂载到容器中

```js
function render(vnode,container){
	mountEl(vnode,container)
}
function mountEl(vnode,container){
	const el=document.createElement(vnode.tag);
	container.appendChild(el)
}
```

这个render函数对于标准html标签是可以正常运作的，但不适用于组件。为了能够渲染组件，我们需要思考：组建的vnode应如何表示？

对于 `html` 标签的 `vnode` 来说，其 `tag` 属性的值就是标签的名字，但如果是组件的话，其 `vnode` 中 `tag` 属性的值应该是什么呢？

很简单，我们可以将组件指向自身

```js
class MyComponent{
render(){
	return {
		tag:'div'
		}
	}
}
```

我们使用class定义了一个类，它是一个组件(有状态组件)，我们用vnode描述它

```js
const componentVnode={
	tag:MyComponent
}
```

> 完整流程

```js
function render(vnode, container) {
  //如果vnode的tag属性值是string，则为html标签
  if (typeof vnode.tag === "string") {
    mountEl(vnode, container);
    //否则为组件
  } else {
    mountComponent(vnode, container);
  }
}

function mountEl(vnode, container) {
  const el = document.createElement(vnode.tag);
  container.appendChild(el);
}

function mountComponent(vnode, container) {
//创建组件实例
  const instance = new vnode.tag();
//渲染  
  instance.$vnode = instance.render();
//挂载  
  mountEl(instance.$vnode, container);
}
//html vnode
const htmlVnode = {
  tag: "h1",
};
//组件 vnode
class MyComponent {
  render() {
    return {
      tag: "h2",
    };
  }
}
const componentVnode = {
  tag: MyComponent,
};
render(htmlVnode, document.querySelector("#app"));
render(componentVnode, document.querySelector("#app"));

```

## 组件的种类

第一种方式是使用一个普通的函数：

```js
function MyComponent(props) {}
```

第二种方式是使用一个类：

```js
class MyComponent {}
```

实际上它们分别代表两类组件：**函数式组件(Functional component)** 和  **有状态组件(Stateful component)** 。

* 函数式组件：
  * 是一个纯函数
  * 没有自身状态，只接收外部数据
  * 产出 `vnode` 的方式：单纯的函数调用
* 有状态组件：
  * 是一个类，可实例化
  * 可以有自身状态
  * 产出 `vnode` 的方式：需要实例化，然后调用其 `render` 函数
