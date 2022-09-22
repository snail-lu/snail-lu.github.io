---
title: Promise实现原理
summary: 阅读ES6-Promise源码从而了解Promise实现原理
tags:
- Promise
- JavaScript
categories:
- [前端进阶]
---

### 1. `Promise` 类实现
```js
// /lib/es6-promise/promise.js

function needsResolver() {
    throw new TypeError('You must pass a resolver function as the first argument to the promise constructor');
}

function needsNew() {
    throw new TypeError("Failed to construct 'Promise': Please use the 'new' operator, this object constructor cannot be called as a function.");
}

class Promise {
    constructor(resolver) {
        // nextId()返回一个自增变量
        // 创建实例标识
        this[PROMISE_ID] = nextId();

        // _result 缓存 promise 结果
        // _state  缓存 promise 的状态
        this._result = this._state = undefined;

        // _subscribers 缓存订阅 promise 的回调 
        this._subscribers = [];

        // 空函数 function noop() {}
        if (noop !== resolver) {
            // 判断resolver是否是一个函数，否则报错
            typeof resolver !== 'function' && needsResolver();

            // 判断Promise是否通过new来调用，否则报错
            // 调用initializePromise函数将resolve函数和reject函数注入到resolver中
            this instanceof Promise ? initializePromise(this, resolver) : needsNew();
        }
    }

    /**
     * @method catch实现
     * @param {Function} onRejection
     * @return {Promise}
     */
    catch(onRejection) {
        // 转调用then方法
        return this.then(null, onRejection);
    }
    /**
     * @method finally实现
     * @param {Function} callback
     * @return {Promise}
     */
    finally(callback) {
        let promise = this;
        let constructor = promise.constructor;

        if ( isFunction(callback) ) {
            return promise.then(value => constructor.resolve(callback()).then(() => value),
                            reason => constructor.resolve(callback()).then(() => { throw reason; }));
        }

        return promise.then(callback, callback);
    }
}

// 将实现的then方法挂载到Promise.prototype上
Promise.prototype.then = then;
export default Promise;

// 将实现的all、race、resolve、reject静态方法挂载Promise上
Promise.all = all;
Promise.race = race;
Promise.resolve = Resolve;
Promise.reject = Reject;
```

### 2. `resolve` 和 `reject` 函数实现
```js
// /lib/es6-promise/-internal.js

/**
 * new Promise((resolve, reject) => {})
 * resolver = (resolve, reject) => {}
 * initializePromise函数来为resolver注入resolve函数和reject函数
 */
function initializePromise(promise, resolver) {
    try {
        // 这里执行了resolver函数
        // resolver中的形参resolve = resolvePromise
        // resolver中的形参reject = rejectPromise
        resolver(function resolvePromise(value){
            // 调用内部实现的resolve函数
            resolve(promise, value);
        }, function rejectPromise(reason) {
            // 调用内部实现的reject函数
            reject(promise, reason);
        });
    } catch(e) {
        reject(promise, e);
    }
}

/**
 * resolve函数实现
 */
function resolve(promise, value) {
    // new Promise((resolve, reject) => {
    //      setTimeout(() => {
    //          resolve('5')
    //      }, 3000)
    // })

    // value === '5'
    // 如果resolve的value是当前promise实例
    if (promise === value) {
        // selfFulfillment为一个报错函数
        // 调用reject方法更新promise状态到REJECTED，返回报错信息
        reject(promise, selfFulfillment());
    } else if (objectOrFunction(value)) {
        // 如果value是否是一个object或function
        let then;
        try {
            then = value.then;
        } catch (error) {
            // 如果value上不带then方法，
            // reject处理promise, 返回报错信息
            reject(promise, error);
            return;
        }
        // value可能是thenable的，进一步处理
        handleMaybeThenable(promise, value, then);
    } else {
        // 其他情况，转换promise状态到FULFILLED
        fulfill(promise, value);
    }
}

/**
 * new Promise((resolve, reject) => {})
 * reject函数实现
 */
function reject(promise, reason) {
    // promise的状态不是pending状态，不予处理
    if (promise._state !== PENDING) { return; }

    // 修改promise实例的状态为REJECTED
    promise._state = REJECTED;

    // 缓存reject的原因
    promise._result = reason;

    // 通知订阅者
    asap(publishRejection, promise);
}

/**
 * 转换promise状态到FULFILLED
 */
function fulfill(promise, value) {
    // promise的状态不是pending状态，不予处理
    if (promise._state !== PENDING) { return; }

    // 缓存promise结果
    promise._result = value;

    // 修改promise状态为FULFILLED
    promise._state = FULFILLED;

    // 通知订阅者
    if (promise._subscribers.length !== 0) {
        asap(publish, promise);
    }
}

```

### 3. `then` 方法实现
```js
// /lib/es6-promise/then.js

export default function then(onFulfillment, onRejection) {
    const parent = this;

    // 创建子promise实例，仅初始化变量，不进行initializePromise处理
    const child = new this.constructor(noop);
    // child: {
    //     [PROMISE_ID]: id++,
    //     _state: undefined,
    //     _result: undefined,
    //     _subscribers: []
    // }

    // 这里是对上一步的补充，避免Promise.prototype.then.call(otherThingWithoutPromiseId)的情况的发生
    if (child[PROMISE_ID] === undefined) {
        makePromise(child);
    }

    // _state: PENDING = 0 | FULFILLED = 1 | REJECTED = 2
    const { _state } = parent;

    // 当前的promise状态已经不是PENDING
    if (_state) {
        // 根据_state 获取 onFulfillment 或者onRejection 回调函数
        const callback = arguments[_state - 1];

        // 更新子promise的状态
        asap(() => invokeCallback(_state, child, callback, parent._result));
    } else {
        // 当前的 promise 为 PENDING 
        // 将子promise 收集到父promise 的 _subscribers 中
        subscribe(parent, child, onFulfillment, onRejection);
    }   

    // 返回子promise，供链式调用
    return child;
}
```

### 4. `subscribe`和 `publish` 函数的实现
```js
// 收集订阅者
function subscribe(parent, child, onFulfillment, onRejection) {
    let { _subscribers } = parent;
    let { length } = _subscribers;

    parent._onerror = null;

    // 收集then方法的创建的子promise及回调，每次增加3个元素
    _subscribers[length] = child;
    _subscribers[length + FULFILLED] = onFulfillment;
    _subscribers[length + REJECTED]  = onRejection;

    // 在 parent 状态更新之后增加的订阅
    // 状态不再发生变化 则直接发出通知
    if (length === 0 && parent._state) {
        asap(publish, parent);
    }
}

// resolve函数调用时会触发fullfill，修改promise状态并进行publish
function publish(promise) {
    let subscribers = promise._subscribers;
    let settled = promise._state;

    if (subscribers.length === 0) { return; }

    let child, callback, detail = promise._result;

    // subscribe中每次增加3个元素，依次为then创建的子promise，传入then方法的onFulfillment函数和onRejection函数
    // 这里每3个为一组，因为是执行resolve，onRejection无需调用
    for (let i = 0; i < subscribers.length; i += 3) {
        child = subscribers[i];
        callback = subscribers[i + settled];

        // 存在子promise 订阅
        if (child) {
            invokeCallback(settled, child, callback, detail);
        } else {
            callback(detail);
        }
    }

    promise._subscribers.length = 0;
}

```

### 参考文档
[1. ES6-Promise源码阅读](https://juejin.cn/post/6844903684904583181)
[2. ES6-Promise源码](https://github.com/stefanpenner/es6-promise)
[3. Promise/A+规范](https://www.ituring.com.cn/article/66566)
[4. 图解 Promise 实现原理](https://zhuanlan.zhihu.com/p/58428287)