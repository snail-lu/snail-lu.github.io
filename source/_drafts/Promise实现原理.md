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
     * @method catch
     * @param {Function} onRejection
     * @return {Promise}
     */
    catch(onRejection) {
        // 转调用then方法
        return this.then(null, onRejection);
    }
    /**
     * @method finally
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
Promise.prototype.then = then;
export default Promise;
Promise.all = all;
Promise.race = race;
Promise.resolve = Resolve;
Promise.reject = Reject;
```

### 2. `resovle` 函数和 `reject` 函数实现
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
### 参考文档
[1. ES6-Promise源码阅读](https://juejin.cn/post/6844903684904583181)
[2. ES6-Promise源码](https://github.com/stefanpenner/es6-promise)
[3. Promise/A+规范](https://www.ituring.com.cn/article/66566)