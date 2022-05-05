---
title: Vue2基础知识总结
date: 2021-05-05 21:11:23
tags:
- Vue
- Vue2
categories:
- [前端]
- [面试]
---
### 1. `v-show` 与 `v-if`的区别
- **v-if**   
“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建；也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
- **v-show**   
不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 `display` 属性进行切换。
综上，`v-if` 适用于在运行时很少改变条件，不需要频繁切换条件的场景；`v-show` 则适用于需要非常频繁切换条件的场景。（微信小程序中的`wx:if`和`wx:hidden`与上述类似）

### 2. 列表渲染中`key`的作用
`key` 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes（虚拟节点）。如果不使用 `key`，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 `key` 时，它会基于 `key` 的变化重新排列元素顺序，并且会移除 `key` 不存在的元素。
综上，简单列表的渲染可以不使用`key`或者用数组的`index`作为`key`（效果等同于不带key），这种模式下性能最高，但是并不能准确的更新列表项的状态。一旦你需要保存列表项的状态，那么就需要用使用唯一的key用来准确的定位每一个列表项以及复用其自身的状态。

#### 4. `computed` 和 `watch` 的区别及各自的应用场景
- **computed**
计算属性，依赖其它属性值，并且 `computed` 的值有缓存，只有它依赖的属性值发生改变才会重新求值；  
- **watch**
更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；  

**运用场景：**  
- 当我们需要进行数值计算，并且依赖于其它数据时，应该使用 `computed`，因为可以利用 `computed` 的缓存特性，避免每次获取值时，都要重新计算；

- 当我们需要在`数据变化时执行异步`或`开销较大的操作`时，应该使用 `watch`，使用watch选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

### 4. Vue 的单向数据流？
所有的 prop 都使得其父子 prop 之间形成了一个**单向下行绑定**：父级 prop 的更新会向下流动到子组件中，但是反过来则不行。这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。
额外的，每次父级组件发生更新时，子组件中所有的 `prop` 都将会刷新为最新的值。这意味着你不应该在一个子组件内部改变 prop。如果你这样做了，Vue会在浏览器的控制台中发出警告。子组件想修改时，只能通过`$emit`派发一个自定义事件，父组件接收到后，由父组件修改。

### 5、 vue实例的生命周期钩子都有哪些？

生命周期 | 描述
---|---
beforeCreate | 组件实例被创建之初，组件的属性生效之前
created | 组件实例已经完全创建，属性也绑定，但真实 dom 还没有生成，$el 还不可用
beforeMount | 在挂载开始之前被调用：相关的 render 函数首次被调用
mounted | el 被新创建的 vm.$el 替换，并挂载到实例上去之后调用该钩子
beforeUpdate | 组件数据更新之前调用，发生在虚拟 DOM 打补丁之前
updated | 组件数据更新之后
activited | keep-alive 专属，组件被激活时调用
deactivated | keep-alive 专属，组件被销毁时调用
beforeDestory | 组件销毁前调用
destoryed | 组件销毁后调用

![image](https://note.youdao.com/yws/api/personal/file/2BC932EC07A94D708B48933FFCF56131?method=download&shareKey=72ed2942cb733bfd27d2d039a1739544)

### 6、 父组件和子组件生命周期钩子执行顺序？
Vue 的父组件和子组件生命周期钩子函数执行顺序可以归类为以下 4 部分：

- 加载渲染过程  
父 beforeCreate -> 父 created -> 父 beforeMount -> 子 beforeCreate -> 子 created -> 子 beforeMount -> 子 mounted -> 父 mounted


- 子组件更新过程  
父 beforeUpdate -> 子 beforeUpdate -> 子 updated -> 父 updated


- 父组件更新过程  
父 beforeUpdate -> 父 updated


- 销毁过程  
父 beforeDestroy -> 子 beforeDestroy -> 子 destroyed -> 父 destroyed

### 7、父组件监听子组件生命周期钩子的实现方法。
- `props/$emit`
```jsx
// Parent.vue
<Child @mounted="doSomething"/>

doSomething() {
   console.log('子组件mounted了');
},
    
// Child.vue
mounted() {
  this.$emit("mounted");
}
```

- `@hook`
```js
//  Parent.vue
<Child @hook:mounted="doSomething" ></Child>

doSomething() {
   console.log('子组件mounted了');
},
    
//  Child.vue
mounted(){
   console.log('子组件触发 mounted 钩子函数 ...');
},  
```

### 8、vue组件间常用的通信方式有哪几种？
- `props/$emit` 父子间通信
- `ref` 与 `$parent / $children` 父子间通信
  - ref：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例
  - $parent / $children：访问父 / 子实例  
- `EventBus($emit / $on)` 父子、隔代、兄弟间通信
```js
// event-bus.js
export const EventBus = new Vue()

// A组件中调用EventBus.$emit(channel: string, [...args])发送消息
sendMsg() {
    EventBus.$emit("aMsg", '来自A页面的消息');
}

// B组件中调用EventBus.$on(channel: string, callback(payload1,…))接收消息
EventBus.$on("aMsg", (msg) => {
    // A发送来的消息
    this.msg = msg;
});
```
- `Vuex` 父子、隔代、兄弟间通信


### 9、vue是如何实现数据双向绑定的？
Vue实现数据双向绑定主要利用的就是: **数据劫持**和**发布订阅模式**。
- **发布订阅模式**  
定义了对象间的一种一对多的关系，让多个观察者对象同时监听某一个主题对象，当一个对象发生改变时，所有依赖于它的对象都将得到通知。
- **数据劫持**  
就是利用JavaScript的访问器属性，即Object.defineProperty()方法，当对对象的属性进行赋值时，Object.defineProperty就可以通过set方法劫持到数据的变化，然后通知发布者(主题对象)去通知所有观察者，观察者收到通知后，就会对视图进行更新。  

Vue 主要通过以下 4 个步骤来实现数据双向绑定的：
- 实现一个**监听器 Observer**  
对数据对象进行遍历，包括子属性对象的属性，利用 Object.defineProperty() 对属性都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到了数据变化。
- 实现一个**编译器 Compiler**  
解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。
- 实现一个**订阅者 Watcher**  
Watcher 订阅者是 Observer 和 Compiler 之间通信的桥梁 ，主要的任务是订阅 Observer 中的属性值变化的消息，当收到属性值变化的消息时，触发编译器 Compiler 中对应的更新函数。
- 实现一个**发布者 Dep**  
订阅器采用 发布-订阅 设计模式，用来收集订阅者 Watcher，对监听器 Observer 和 订阅者 Watcher 进行统一管理。

![image](https://note.youdao.com/yws/api/personal/file/WEB04c43c43cde70438811ebae027344add?method=download&shareKey=ac2f89c46ce2638c34dc2e069ba7dc9d)

总之就是，在创建Vue实例的时候给传入的data进行数据劫持，同时视图编译的时候，对于使用到data中数据的地方进行创建Watcher对象，然后在数据劫持的getter中添加到发布者对象中，当劫持到数据发生变化的时候，就通过发布订阅模式以回调函数的方式通知所有观察者操作DOM进行更新，从而实现数据的双向绑定。

#### 10、data为什么是一个函数而不是一个对象？
因为组件可能被用来创建多个实例。如果data仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供data函数，每次创建一个新实例后，我们能够调用data函数，从而返回初始数据的一个全新副本数据对象，这样就保证了每个组件的data独立性。

#### 11、Vue的diff算法。

- 特点：    
  - 比较只会在同层级进行, 不会跨层级比较。
  - 在 diff 比较的过程中，循环从两边向中间收拢。

- 流程
1. 首先会对新老 VNode 的开始和结束位置进行标记：oldStartIdx、oldEndIdx、newStartIdx、newEndIdx。
2. 进入到的 while 循环处理中，这里是 diff 算法的核心流程，分情况进行了新老节点的比较并移动对应的 VNode 节点。while 循环的退出条件是直到老节点或者新节点的开始位置大于结束位置。  
   while 循环中的处理逻辑， 循环过程中首先对新老 VNode 节点的头尾进行比较，寻找相同节点，如果有相同节点满足 sameVnode（可以复用的相同节点） 则直接进行 patchVnode (该方法进行节点复用处理)，并且根据具体情形，移动新老节点的 VNode 索引，以便进入下一次循环处理，一共有 2 * 2 = 4 种情形。
   - **情形一**：当新老 VNode 节点的 start 满足 sameVnode 时，直接 patchVnode 即可，同时新老 VNode 节点的开始索引都加 1。
   - **情形二**：当新老 VNode 节点的 end 满足 sameVnode 时，同样直接 patchVnode 即可，同时新老 VNode 节点的结束索引都减 1。
   - **情形三**：当老 VNode 节点的 start 和新 VNode 节点的 end 满足 sameVnode 时，这说明这次数据更新后 oldStartVnode 已经跑到了 oldEndVnode 后面去了。这时候在 patchVnode 后，还需要将当前真实 dom 节点移动到 oldEndVnode 的后面，同时老 VNode 节点开始索引加 1，新 VNode 节点的结束索引减 1。
   - **情形四**：当老 VNode 节点的 end 和新 VNode 节点的 start 满足 sameVnode 时，这说明这次数据更新后 oldEndVnode 跑到了 oldStartVnode 的前面去了。这时候在 patchVnode 后，还需要将当前真实 dom 节点移动到 oldStartVnode 的前面，同时老 VNode 节点结束索引减 1，新 VNode 节点的开始索引加 1。  
   如果都不满足以上四种情形，那说明没有相同的节点可以复用。于是则通过查找事先建立好的以旧的 VNode 为 key 值，对应 index 序列为 value 值的哈希表。从这个哈希表中找到与 newStartVnode 一致 key 的旧的 VNode 节点，如果两者满足 sameVnode 的条件，在进行 patchVnode 的同时会将这个真实 dom 移动到 oldStartVnode 对应的真实 dom 的前面；如果没有找到，则说明当前索引下的新的 VNode 节点在旧的 VNode 队列中不存在，无法进行节点的复用，那么就只能调用 createElm 创建一个新的 dom 节点放到当前 newStartIdx 的位置。
3. 当 while 循环结束后，根据新老节点的数目不同，做相应的节点添加或者删除。若新节点数目大于老节点则需要把多出来的节点创建出来加入到真实 dom 中，反之若老节点数目大于新节点则需要把多出来的老节点从真实 dom 中删除。至此整个 diff 过程就已经全部完成了。


#### 12、Vue项目中做过的优化。
- 编码优化
  - 尽量不要将所有的数据都放在data中，data中的数据都会增加getter和setter，会收集对应的 watcher
  - vue 在 v-for 时给每项元素绑定事件尽量用事件代理
  - 拆分组件( 提高复用性、增加代码的可维护性,减少不必要的渲染 )
  - v-if 当值为false时内部指令不会执行,具有阻断功能，很多情况下使用v-if替代v-show
  - 合理使用路由懒加载、异步组件
  - Object.freeze 冻结数据
- 用户体验
  - app-skeleton 骨架屏
  - pwa serviceworker
- 加载性能优化
  - 第三方模块按需导入 ( babel-plugin-component )
  - 滚动到可视区域动态加载 ( https://tangbc.github.io/vue-virtual-scroll-list )
  - 图片懒加载 (https://github.com/hilongjw/vue-lazyload.git)
- SEO 优化
  - 预渲染插件 prerender-spa-plugin
  - 服务端渲染 ssr
- 打包优化
  - 使用 cdn 的方式加载第三方模块
  - 多线程打包 happypack、parallel-webpack
  - 控制包文件大小（tree shaking / splitChunksPlugin）
  - 使用DllPlugin提高打包速度
- 缓存/压缩
  - 客户端缓存/服务端缓存
  - 服务端gzip压缩