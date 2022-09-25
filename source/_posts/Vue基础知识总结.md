---
title: Vue基础知识总结
date: 2021-05-05 21:11:23
summary: Vue基础知识
tags:
- Vue
- Vue2
- 面试
categories:
- [前端进阶]
- [读书笔记]
---
### 1. `v-show` 与 `v-if`的区别
- **v-if**   
“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建；也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。
- **v-show**   
不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 的 `display` 属性进行切换。
综上，`v-if` 适用于在运行时很少改变条件，不需要频繁切换条件的场景；`v-show` 则适用于需要非常频繁切换条件的场景。（微信小程序中的`wx:if`和`wx:hidden`与上述类似）

### 2. 列表渲染中`key`的作用
`key` 主要用在 Vue 的虚拟 DOM 算法，在新旧 nodes 对比时辨识 VNodes（虚拟节点）。如果不使用 `key`，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。而使用 `key` 时，它会基于 `key` 的变化重新排列元素顺序，并且会移除 `key` 不存在的元素。
综上，简单列表的渲染可以不使用`key`或者用数组的`index`作为`key`（效果等同于不带key），这种模式下性能最高，但是并不能准确的更新列表项的状态。一旦你需要保存列表项的状态，那么就需要用使用唯一的key用来准确的定位每一个列表项以及复用其自身的状态。否则就可能会出现错乱的情况。

#### 3. `computed` 和 `watch` 的区别及应用场景
- **computed**
计算属性，依赖其它属性值，并且 `computed` 的值有缓存，只有它依赖的属性值发生改变才会重新求值；（源码中通过`dirty`属性来控制是重新计算还是从缓存中取值）
- **watch**
更多的是「观察」的作用，类似于某些数据的监听回调 ，每当监听的数据变化时都会执行回调进行后续操作；  

- **应用场景**  
  - 需要进行数值计算，并且依赖于其它数据，应该使用 `computed`，因为可以利用 `computed` 的缓存特性，避免每次获取值时，都要重新计算；

  - 需要在数据变化时执行`异步`或`开销较大的操作`，应该使用 `watch`。因为使用`watch`选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这些都是计算属性无法做到的。

### 4. 单向数据流
从父组件传到子组件的数据，子组件没有权利直接修改，想要修改该数据，只能通过`$emit`派发一个自定义事件，父组件接收到后，由父组件修改。
每次父级组件发生变更时，子组件中所有的 prop 都将会刷新为最新的值。这样会防止从子组件意外改变父级组件的状态，从而导致应用的数据流向难以理解。

### 5. Vue实例的生命周期钩子

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
beforeDestroy | 组件销毁前调用
destroyed | 组件销毁后调用
errorCaptured | 2.5.0+ 新增，在捕获一个来自后代组件的错误时被调用。

![image](https://note.youdao.com/yws/api/personal/file/2BC932EC07A94D708B48933FFCF56131?method=download&shareKey=72ed2942cb733bfd27d2d039a1739544)

### 6. 父组件和子组件生命周期钩子执行顺序
可以归类为以下 4 部分：

- 加载渲染过程  
父 `beforeCreate` -> 父 `created` -> 父 `beforeMount` -> 子 `beforeCreate` -> 子 `created` -> 子 `beforeMount` -> 子 `mounted` -> 父 `mounted`


- 子组件更新过程  
父 `beforeUpdate` -> 子 `beforeUpdate` -> 子 `updated` -> 父 `updated`


- 父组件更新过程  
父 `beforeUpdate` -> 父 `updated`


- 销毁过程  
父 `beforeDestroy` -> 子 `beforeDestroy` -> 子 `destroyed` -> 父 `destroyed`

### 7. 父组件中监听子组件生命周期钩子
- `props/$emit`
```vue
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
```vue
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

### 8. 组件间的通信方式
- `props/$emit` 父子间通信
- `ref` 与 `$parent / $children` 父子间通信
  - `ref`：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例
  - `$parent` / `$children`：访问父 / 子组件实例  
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


### 9. Vue2数据响应式原理
`Vue2`数据响应式主要利用的就是: `数据劫持`和`发布订阅模式`。
- **发布订阅模式**  
当发布者数据变化时发布数据，全部订阅者均可以接收到通知。
- **数据劫持**  
就是利用JavaScript的访问器属性，即`Object.defineProperty()`方法，当对对象的属性进行赋值时，`Object.defineProperty`就可以通过`set`方法劫持到数据的变化，然后通知发布者(主题对象)去通知所有订阅者，订阅者收到通知后，就会对视图进行更新。  

Vue2 响应式的实现主要有：
- **监听器 Observer**
对数据对象进行遍历，包括子属性对象的属性，利用 `Object.defineProperty()` 对属性都加上 `setter` 和 `getter`。这样的话，给这个对象的某个属性赋值，就会触发 `setter`，那么就能监听到了数据变化。
- **依赖管理器 Dep**
用来收集订阅者 `Watcher`，对监听器 `Observer` 和 订阅者 `Watcher` 进行统一管理。
- **订阅者 Watcher**
`Watcher` 是 `Observer` 和 `Compiler` 之间通信的桥梁 ，主要的任务是订阅 `Observer` 中的属性值变化的消息，当收到属性值变化的消息时，触发编译器 `Compiler` 中对应的更新函数。
- **编译器 Compiler**
解析 Vue 模板指令，将模板中的变量都替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据有变动，收到通知，调用更新函数进行数据更新。

![image](https://note.youdao.com/yws/api/personal/file/WEB04c43c43cde70438811ebae027344add?method=download&shareKey=ac2f89c46ce2638c34dc2e069ba7dc9d)

总之就是，在创建 Vue 实例的时候给传入的 `data`的每个属性使用 `Observer` 增加`getter` 和 `setter`，同时视图编译的时候，对于使用到`data`中数据的地方进行创建 `Watcher` ，然后在数据劫持的 `getter` 中收集 `Watcher` 到订阅器 `Dep`，当劫持的数据发生变化的时候，触发`setter`，`setter中` 会调用 `Dep` 来通知所有收集到的 `Watcher`，`Watcher` 通知DOM进行更新，从而实现数据的响应式变化。

### 10. 双向数据绑定原理
表单元素 `<input>`、`<textarea>` 及 `<select>` 上可以用 `v-model` 指令创建双向数据绑定，当进行表单输入或选择的时候，通过`v-model`绑定的值会同步修改。
`v-model` 在内部为不同的输入元素使用不同的 `property` 并抛出不同的事件：

- `text` 和 `textarea` 元素使用 `value` property 和 `input` 事件；
- `checkbox` 和 `radio` 使用 `checked` property 和 `change` 事件；
- `select` 字段将 `value` 作为 prop 并将 `change` 作为事件。

### 10. Vue3的响应式和Vue2的区别
Vue3的响应式不再通过`Object.defineProperty()`来对数据进行劫持，而是通过`Proxy`代理实现数据的变化监听。
主要区别：
1. `Proxy`是对整个对象的代理，而`Object.defineProperty`只能代理某个属性。
2. 对象上新增属性，`Proxy`可以监听到，`Object.defineProperty`不能。
3. 数组新增元素，`Proxy`可以监听到，`Object.defineProperty`不能。
4. 若对象内部属性要全部递归代理，`Proxy`可以只在调用的时候递归，而`Object.definePropery`需要一次完成所有递归，性能比Proxy差。
5. `Proxy`在ie浏览器上存在兼容问题


### 11. data为何是一个函数而非对象
因为组件可能被用来创建多个实例。如果data仍然是一个纯粹的对象，则所有的实例将共享引用同一个数据对象！通过提供 `data` 函数，每次创建一个新实例后，我们能够调用 `data` 函数，从而返回初始数据的一个全新副本数据对象，这样就保证了每个组件的data独立性。

### 12. 虚拟DOM
所谓**虚拟DOM**，就是用一个 `JS` 对象来描述一个 `DOM` 节点。
```js
// 示例
<div class="a" id="b">Hello World!</div>

// 上述html代码可以使用如下js对象来表示
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
#### 产生的原因及用途
直接操作 `DOM` 是十分消耗性能的，可以利用 `js` 计算对比数据变化前后的状态，计算出视图中哪些地方需要更新，只更新需要更新的地方，以此来减少不必要的 `DOM` 操作。

#### diff 算法
- **特点：**    
  - 比较只会在同层级进行, 不会跨层级比较。
  - 在 `diff` 比较的过程中，循环从两边向中间收拢。

- **流程：**
1. 首先会对新老 `VNode` 的开始和结束位置进行标记：`oldStartIdx`、`oldEndIdx`、`newStartIdx`、`newEndIdx`。
2. 进入到的 `while` 循环处理中，这里是 `diff` 算法的核心流程，分情况进行了新老节点的比较并移动对应的 `VNode` 节点。`while` 循环的退出条件是直到老节点或者新节点的开始位置大于结束位置。  
`while` 循环中的处理逻辑，循环过程中首先对新老 `VNode` 节点的头尾进行比较，寻找相同节点，如果有相同节点满足 `sameVnode`（可以复用的相同节点） 则直接进行 `patchVnode` (该方法进行节点复用处理)，并且根据具体情形，移动新老节点的 `VNode` 索引，以便进入下一次循环处理，一共有 `2 * 2 = 4` 种情形。
   - **情形一**：当新老 `VNode` 节点的 `start` 满足 `sameVNode` 时，直接 `patchVNode` 即可，同时新老 `VNode` 节点的开始索引都加 1。
   - **情形二**：当新老 `VNode` 节点的 `end` 满足 `sameVNode` 时，同样直接 `patchVNode` 即可，同时新老 `VNode` 节点的结束索引都减 1。
   - **情形三**：当老 `VNode` 节点的 `start` 和新 `VNode` 节点的 `end` 满足 `sameVnode` 时，这说明这次数据更新后 `oldStartVNode` 已经跑到了 `oldEndVNode` 后面去了。这时候在 `patchVNode` 后，还需要将当前真实 `dom` 节点移动到 `oldEndVNode` 的后面，同时老 `VNode` 节点开始索引`加1`，新 `VNode` 节点的结束索引`减1`。
   - **情形四**：当老 `VNode` 节点的 `end` 和新 `VNode` 节点的 `start` 满足 `sameVnode` 时，这说明这次数据更新后 `oldEndVNode` 跑到了 `oldStartVNode` 的前面去了。这时候在 `patchVNode` 后，还需要将当前真实 `dom` 节点移动到 `oldStartVNode` 的前面，同时老 `VNode` 节点结束索引`减1`，新 `VNode` 节点的开始索引`加 1`。  

   如果都不满足以上四种情形，那说明没有相同的节点可以复用。于是则通过查找事先建立好的以旧的 `VNode` 为 `key` 值，对应 `index` 序列为 `value` 值的哈希表。从这个哈希表中找到与 `newStartVnode` 一致 `key` 的旧的 `VNode` 节点，如果两者满足 `sameVnode` 的条件，在进行 `patchVnode` 的同时会将这个真实 `dom` 移动到 `oldStartVnode` 对应的真实 `dom` 的前面；如果没有找到，则说明当前索引下的新的 `VNode` 节点在旧的 `VNode` 队列中不存在，无法进行节点的复用，那么就只能调用 `createElm` 创建一个新的 `dom` 节点放到当前 `newStartIdx` 的位置。

3. 当 `while` 循环结束后，根据新老节点的数目不同，做相应的节点添加或者删除。若新节点数目大于老节点则需要把多出来的节点创建出来加入到真实 `dom` 中，反之若老节点数目大于新节点则需要把多出来的老节点从真实 `dom` 中删除。至此整个 diff 过程就已经全部完成了。


### 13. `$nextTick`原理
> Vue 在更新 DOM 时是异步执行的。只要侦听到数据变化，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据变更。如果同一个 watcher 被多次触发，只会被推入到队列中一次。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作是非常重要的。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际 (已去重的) 工作。Vue 在内部对异步队列尝试使用原生的 Promise.then、MutationObserver 和 setImmediate，如果执行环境不支持，则会采用 setTimeout(fn, 0) 代替。

`$nextTick`的实现本质上就是将某个任务依次尝试使用`Promise.then`、`MutationObserver`和`setImmediate`、`setTimeout`进行处理，前两个方法会将任务放到微任务队列，后两个方法会将任务放到宏任务队列，无论是微任务还是宏任务，该任务都是在DOM更新之后执行的，这样就实现了DOM更新之后再去执行特定任务。

### 14. 数组和对象的变化检测
Vue对数组的7个方法`push`、`pop`、`shift`、`unshift`、`splice`、`sort`、`reverse`实现了响应式，对对象已有属性也是实现了响应式的。但是由于 Vue 会在初始化实例时对 `property` 执行 `getter/setter` 转化，所以 `property` 必须在 `data` 对象上存在才能让 `Vue` 将它转换为响应式的。这导致了以下情况：
- Vue 无法检测对象属性的添加或移除。
- Vue 不能检测以下情况数组的变动：
  - 利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`（其实对于已有元素是可以实现的，但是由于性能问题没有实现）
  - 修改数组的长度时，例如：`vm.items.length = newLength`

出现以上问题可以使用`$set`方法进行响应式处理。

### 15. 项目中做过的优化
- 编码优化
  - 尽量不要将所有的数据都放在 `data` 中，`data`中的数据都会增加 `getter` 和 `setter`，会收集对应的 `watcher`
  - vue 在 `v-for` 时给每项元素绑定事件尽量用事件代理（事件处理绑定在父元素上，而非子元素上）
  - 拆分组件( 提高复用性、增加代码的可维护性,减少不必要的渲染 )
  - `v-if` 当值为 `false` 时内部指令不会执行，具有阻断功能，很多情况下使用`v-if`替代`v-show`
  - 合理使用路由懒加载、异步组件
  - 对于一个巨大的对象或数组数据，如果确信该数据不会修改，使用`Object.freeze`冻结数据，该数据不会被vue监听，能大幅提升性能。
- 用户体验
  - `app-skeleton` 骨架屏
  - `PWA` 与 `Service worker`实现离线访问和后台同步
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

### 16. Vue的初始化过程（new Vue(options)）
在使用 `new Vue(options)` 创建Vue实例时，会调用 Vue 原型对象上的 `_init` 方法，执行一系列初始化操作。
```js
// Vue v2.6.14
Vue.prototype._init = function (options?: Object) {
  const vm: Component = this
  // a uid
  vm._uid = uid++
  let startTag, endTag
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    startTag = `vue-perf-start:${vm._uid}`
    endTag = `vue-perf-end:${vm._uid}`
    mark(startTag)
  }
  // a flag to avoid this being observed
  vm._isVue = true
  // merge options
  // 普通组件
  if (options && options._isComponent) {
    // optimize internal component instantiation
    // since dynamic options merging is pretty slow, and none of the
    // internal component options needs special treatment.
    initInternalComponent(vm, options)
  } else {
    // 根组件merge options
    vm.$options = mergeOptions(
      resolveConstructorOptions(vm.constructor),
      options || {},
      vm
    )
  }
  /* istanbul ignore else */
  if (process.env.NODE_ENV !== 'production') {
    initProxy(vm)
  } else {
    vm._renderProxy = vm
  }
  // expose real self
  vm._self = vm
  initLifecycle(vm) // 给vue实例上挂载$parent, $root, $refs, $children等属性
  initEvents(vm) // 初始化实例的事件系统
  initRender(vm) // 初始化渲染函数，给实例上挂载$createElement方法
  callHook(vm, 'beforeCreate') // 触发beforeCreate钩子
  initInjections(vm) // 初始化injections
  initState(vm) // 初始化props,methods,data,computed,watch
  initProvide(vm) // 初始化 provide
  callHook(vm, 'created') // 触发created钩子
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    vm._name = formatComponentName(vm, false)
    mark(endTag)
    measure(`vue ${vm._name} init`, startTag, endTag)
  }

  // 判断用户是否传入了el选项，如果传入了则调用$mount函数进入模板编译与挂载阶段，
  // 如果没有传入el选项，则不进入下一个生命周期阶段，需要用户手动执行 vm.$mount 方法才进入下一个生命周期阶段。
  if (vm.$options.el) {
    vm.$mount(vm.$options.el)
  }
}
```
由上，可以总结出vue初始化的流程如下：
1. 处理组件配置项，合并options后挂载到 `vm.$options`
2. 调用一些初始化函数: `initLifecycle` 、`initEvents`、`initRender`
3. 触发 `beforeCreate` 生命周期钩子
4. 调用另外一些初始化函数: `initInjections` 、`initState`、`initProvide`
5. 触发 `created` 生命周期钩子
6. 调用 `$mount` 进入挂载阶段