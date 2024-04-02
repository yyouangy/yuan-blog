---
date: 2022-05-14 22:53:00
title: Js树结构操作
---

我们前端工程师日常开发中经常用到树形结构数据，如多级菜单，商品的多级分类等等，然后数据库的设计往往是扁平结构，此时前端可能就要经常处理树形数据，本文尝试总结一下具体的处理思路。
<img  src="/_nuxt/img/blog/tree.png"/>

## 一、树结构遍历

### 1.树结构介绍

Js 中的树结构一般是类似于这样的结构：

```js
let tree = [
  {
    id: "1",
    title: "节点1",
    children: [
      {
        id: "1-1",
        title: "节点1-1",
      },
      {
        id: "1-2",
        title: "节点1-2",
      },
    ],
  },
  {
    id: "2",
    title: "节点2",
    children: [
      {
        id: "2-1",
        title: "节点2-1",
        children: [
          {
            id: "2-1-1",
            title: "节点2-1-1",
          },
        ],
      },
    ],
  },
];
```

第一层为树结构的根节点，每个节点的 children 属性(如果有)是一棵子树，如果没有 children 属性，代表该节点是叶子节点。

### 2.树结构遍历方法

树结构的常用场景之一就是遍历，而遍历又分为广度优先遍历、深度优先遍历。其中深度优先遍历是可递归的，而广度优先遍历是非递归的，通常用循环来实现。深度优先遍历又分为先序遍历、后序遍历，二叉树还有中序遍历，实现方法可以是递归，也可以是循环。
<img  src="/_nuxt/img/blog/tree1.png"/>

广度优先和深度优先的概念很简单，区别如下：

- `广度优先`：即访问树结构的第 n+1 层前必须先访问完第 n 层
- `深度优先`：访问完一颗子树再去访问后面的子树，而访问子树的时候，先访问根再访问根的子树，称为先序遍历；先访问子树再访问根，称为后序遍历

#### 2.1 广度优先遍历-递归实现

广度优先的思路是维护一个队列，队列的初始值为树结构根节点组成的列表，重复执行以下步骤直到队列为空：

- 取出队列第一个元素，进行访问相关操作，然后将其子节点(如果有)追加到队列最后
  下面是代码实现，我们将对数组的访问操作提供回调函数供使用者调用

```js
//广度优先
function treeBfs(tree, fn) {
  let node = {};
  let list = [...tree];
  while ((node = list.shift())) {
    fn(node);
    node.children && list.push(...node.children);
  }
}
```

我们调用 treeBfs 函数，发现打印如下，可以看到访问完第 n 层元素后才会访问第 n+1 层元素

```js
treeBfs(tree, (node) => console.log(node.title));

// 节点1
// 节点2
// 节点1-1
// 节点1-2
// 节点2-1
// 节点2-1-1
```

#### 2.2 深度优先遍历-递归实现

先序遍历

```js
function recursionDfs(tree, fn) {
  tree.forEach((item) => {
    fn(item);
    item.children && recursionDfs(item.children, fn);
  });
}
```
后序遍历与先序遍历相比，只是调换一下节点遍历和子树遍历的顺序

```js
function recursionDfs(tree, fn) {
  tree.forEach((item) => {
    item.children && recursionDfs(item.children, fn);
    fn(item);
  });
}
```
测试

```js
recursionDfs(tree, (node) => console.log(node.title));

// 节点1
// 节点1-1
// 节点1-2
// 节点2
// 节点2-1
// 节点2-1-1
```
