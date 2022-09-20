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
 * resolve函数实现
 */
function resolve(promise, value) {
    if (promise === value) {
        reject(promise, selfFulfillment());
    } else if (objectOrFunction(value)) {
        let then;
        try {
            then = value.then;
        } catch (error) {
            reject(promise, error);
            return;
        }
        handleMaybeThenable(promise, value, then);
    } else {
        fulfill(promise, value);
    }
}

/**
 * new Promise((resolve, reject) => {})
 * reject函数实现
 */
function reject(promise, reason) {
    if (promise._state !== PENDING) { return; }
    promise._state = REJECTED;
    promise._result = reason;

    asap(publishRejection, promise);
}

/**
 * 转换promise状态到FULFILLED
 */
function fulfill(promise, value) {
    // 判断promise的状态是否是pending状态，否则不予修改
    if (promise._state !== PENDING) { return; }

    // 缓存promise结果
    promise._result = value;

    // 修改promise状态为FULFILLED
    promise._state = FULFILLED;

    // 通知订阅序列
    if (promise._subscribers.length !== 0) {
        asap(publish, promise);
    }
}

```
### 参考文档
[1. ES6-Promise源码阅读](https://juejin.cn/post/6844903684904583181)
[2. ES6-Promise源码](https://github.com/stefanpenner/es6-promise)
[3. Promise/A+规范](https://www.ituring.com.cn/article/66566)