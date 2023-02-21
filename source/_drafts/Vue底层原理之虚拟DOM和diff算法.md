---
title: Vue底层原理之虚拟DOM和diff算法
tags:
---

### 1. 虚拟DOM
- **概念**：就是用一个 `JS` 对象来描述一个 `DOM` 节点
- **用途**：直接操作 `DOM` 是十分消耗性能的，生成虚拟DOM后，可以利用 `js` 计算对比数据变化前后的状态，计算出视图中哪些地方需要更新，只更新需要更新的地方，以此来减少不必要的 `DOM` 操作。
- **产生**：虚拟DOM是通过渲染函数（h函数）生成的

示例：
```html
<div class="a" id="b">Hello World!</div>
```
上述 `HTML` 元素可以使用如下 `js` 对象来表示：
```js
{
  tag: 'div',  // 元素标签
  attrs: {           // 属性
    class: 'a',
    id: 'b'
  },
  text: 'Hello World!',  // 文本内容
  children: []       // 子元素
}
```

### 2. diff 算法
#### 产生
借鉴了著名的虚拟DOM库 [snabbdom](https://github.com/snabbdom/snabbdom)，用于比较新旧虚拟DOM的变化以实现最小量更新。
#### 特点  
- 比较只会在同层级进行, 不会跨层级比较。
- 在 `diff` 比较的过程中，循环从两边向中间收拢。

#### 流程
1. 首先会对新老 `VNode` 的开始和结束位置进行标记：`oldStartIdx`、`oldEndIdx`、`newStartIdx`、`newEndIdx`。
2. 进入到的 `while` 循环处理中，这里是 `diff` 算法的核心流程，分情况进行了新老节点的比较并移动对应的 `VNode` 节点。`while` 循环的退出条件是直到老节点或者新节点的开始位置大于结束位置。  
`while` 循环中的处理逻辑，循环过程中首先对新老 `VNode` 节点的头尾进行比较，寻找相同节点，如果有相同节点满足 `sameVnode`（选择器相同且key相同） 则直接进行 `patchVnode` (对比虚拟dom，更新节点内容或子节点)，并且根据具体情形，移动新老节点的 `VNode` 索引，以便进入下一次循环处理，依次按照下面4种情形进行命中查找：
   - **情形一**：`newStartIdx`节点和`oldStartIdx`节点进行比较，若满足 `sameVNode`，直接 `patchVNode`，同时`newStartIdx` 和 `oldStartIdx` 都加 1。
   - **情形二**：`newEndIdx` 节点和`oldEndIdx`节点进行比较， 若满足 `sameVNode`，直接 `patchVNode`，同时 `newEndIdx` 和 `oldEndIdx` 都减 1。
   - **情形三**：`newEndIdx` 节点和 `oldStartIdx` 节点进行比较，若满足 `sameVnode` ，进行`patchVNode` 后，还需要将`oldStartIdx`节点对应的真实 `dom` 节点移动到`oldEndIdx`节点对应的真实DOM的后面，同时`oldStartIdx + 1`、`newEndIdx - 1`。
   - **情形四**：`newStartIdx`节点和 `oldEndIdx`节点进行比较，若满足 `sameVnode` ，进行 `patchVNode` 后，还需要将`oldEndIdx`节点对应的真实 `dom` 节点移动到 `oldStartIdx`节点对应的真实DOM的前面，同时`oldEndIdx - 1`、 `newStartIdx + 1`。  

   如果以上四种情形没有匹配到节点，那说明新旧节点的首尾暂时没有节点可以复用。于是则通过查找事先建立好的以旧的 `VNode` 为 `key` 值，对应 `index` 序列为 `value` 值的哈希表。从这个哈希表中找到与 `newStartVnode` 一致 `key` 的旧的 `VNode` 节点，如果两者满足 `sameVnode` 的条件，在进行 `patchVnode` 的同时会将这个真实 `dom` 移动到 `oldStartVnode` 对应的真实 `dom` 的前面；如果没有找到，则说明当前索引下的新的 `VNode` 节点在旧的 `VNode` 队列中不存在，无法进行节点的复用，那么就只能调用 `createElm` 创建一个新的 `dom` 节点放到当前 `newStartIdx` 的位置。
   
3. 当 `while` 循环结束后，根据新老节点的数目不同，做相应的节点添加或者删除。若新节点数目大于老节点则需要把多出来的节点创建出来加入到真实 `dom` 中，反之若老节点数目大于新节点则需要把多出来的老节点从真实 `dom` 中删除。至此整个 diff 过程就已经全部完成了。