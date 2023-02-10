---
title: think of javascript
date: 2022-11-22 15:26:26
tags: [javascript]
categories: javascript
---

# js

#### compose

> ES5

```javascript
function compose() {
    var args = Array.prototype.slice.call(arguments);
    return function (x) {
        return args.reduceRight(function (a, b) {
            return b(a);
        }, x);
    }
}
```

> ES6

```javascript
const compose = (...args) => x => args.reduceRight((a, b) => b(a), x);
```

#### pipe

> ES5

```javascript
function pipe() {
    var args = Array.prototype.slice.call(arguments);
    return function (x) {
        return args.reduce(function (a, b) {
            return b(a);
        }, x);
    }
}
```

> ES6

```javascript
const pipe = (...args) => x => args.reduce((a, b) => b(a), x);
```

#### flat

> ES5

```javascript
function flat(source, initial) {
    var start = initial || [];
    return source.reduce(function (a, b) {
        if (Array.isArray(b)) {
            return flat(b, a);
        }
        return a.concat(b);
    }, start);
}
```

> ES6

```javascript
const flat = (source, inital = []) => source.reduce((a, b) => Array.isArray(b) ? flat(b, a) : a.concat(b), inital);
```

#### layer Flat

> ES5

```javascript
function flat(source, layer, initial) {
    var start = initial || [];
    return source.reduce(function (a, b) {
        if (Array.isArray(b) && layer > 1) {
            return flat(b, layer - 1, a);
        }
        return a.concat(b);
    }, start);
}
```

> ES6

```javascript
const flat = (source, layer = 1, initial = []) => source.reduce((a, b) => (Array.isArray(b) && layer > 1) ? flat(b, layer - 1, a) : a.concat(b), initial);
```

#### fibonacci

> ES5

```javascript
function fibonacci(n) {
    if (n === 1 || n === 0) {
        return n;
    }
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

> ES6

```javascript
const fibonacci = n => (n === 1 || n === 0) ? n : (fibonacci(n - 1) + fibonacci(n - 2));
```

#### tailCall Fibonacci

> ES5

```javascript
function fibonacci(n, n1, n2) {
    if (typeof n1 === 'undefined' && typeof n2 === 'undefined') {
        n1 = 0;
        n2 = 1;
    }
    if (n === 0) {
        return n1;
    }
    return fibonacci(n - 1, n2, n1 + n2);

}
```

> ES6

```javascript
const fibonacci = (n, n1 = 0, n2 = 1) => (n === 0) ? n1 : fibonacci(n - 1, n2, n1 + n2);
```

#### cache Fibonacci

> ES5

```javascript
function memo(fn, hasher) {
    function memoFunc() {
        var args = Array.prototype.slice.call(arguments),
            cache = memoFunc.cache,
            hashKey = hasher ? hasher.apply(this, args) : args[0];
        if (!cache[hashKey]) {
            cache[hashKey] = fn.apply(this, args);
        }
        return cache[hashKey];
    }

    memoFunc.cache = {};
    return memoFunc;
}

function fibonacci(n) {
    if (n === 1 || n === 0) {
        return n;
    }
    return fibonacci(n - 1) + fibonacci(n - 2);
}
```

> ES6

```javascript
const memo = (fn, hasher) => {
    const memoFunc = (...args) => {
        const cache = memoFunc.cache,
            hashKey = hasher ? hasher(...args) : args[0];
        if (!cache[hashKey]) {
            cache[hashKey] = fn(...args);
        }
        return cache[hashKey];
    };
    memoFunc.cache = {};
    return memoFunc;
};

const fibonacci = n => (n === 0 || n === 1) ? n : (fibonacci(n - 1) + fibonacci(n - 2));
```

#### curry

> ES5

```javascript
function curry(fn) {
    var paramsLength = fn.length;
    return function callee() {
        var args = Array.prototype.slice.call(arguments),
            length = args.length;
        if (length >= paramsLength) {
            return fn.apply(this, args);
        } else {
            return function () {
                var calleeArgs = Array.prototype.slice.call(arguments);
                return callee.apply(this, args.concat(calleeArgs));
            }
        }
    };
}
```

> ES6

```javascript
const curry = (fn) => {
    const paramsLength = fn.length;
    return function callee(...args) {
        const {length} = args;
        if (length >= paramsLength) {
            return fn(...args);
        } else {
            return (...calleeArgs) => {
                return callee(...[...args, ...calleeArgs]);
            }
        }
    };
};
```

#### debounce

> ES5

```javascript
function debounce(fn, timeout) {
    var timer = null;
    return function () {
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }
        var self = this,
            args = Array.prototype.slice.call(this);
        timer = setTimeout(function () {
            fn.apply(self, args);
        }, timeout);
    }
}
```

> ES6

```javascript
const debounce = (fn, timeout) => {
    let timer = null;
    return (...args) => {
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }
        timer = setTimeout(() => {
            fn(...args);
        }, timeout);
    };
}
```

#### throttle

> ES5

```javascript
function throttle(fn, timeout) {
    var timer = null,
        first = true;
    return function () {
        var self = this,
            args = Array.prototype.slice.call(arguments);
        if (timer) {
            return false;
        }
        if (first) {
            fn.apply(self, args);
            first = false;
            return true;
        }
        timer = setTimeout(() => {
            fn.apply(self, args);
            clearTimeout(timer);
            timer = null;
        }, timeout);
    }
}
```

> ES6

```javascript
const throttle = (fn, timeout) => {
    let timer = null,
        first = true;
    return (...args) => {
        if (timer) {
            return false;
        }
        if (first) {
            fn(...args);
            first = false;
            return true;
        }
        timer = setTimeout(() => {
            fn(...args);
            clearTimeout(timer);
            timer = null;
        }, timeout);
    };
}
```

#### factorial

> ES5

```javascript
function factorial(n) {
    if (n === 1) {
        return n;
    }
    return n * factorial(n - 1);
}
```

> ES6

```javascript
const factorial = n => (n === 1) ? n : n * factorial(n - 1);
```

#### tailCall Factorial

> ES5

```javascript
function factorial(n, p) {
    if (typeof p === 'undefined') {
        p = 1;
    }
    if (n === 1) {
        return p;
    }
    return factorial(n - 1, p * n);
}
```

> ES6

```javascript
const factorial = (n, p = 1) => n === 1 ? p : factorial(n - 1, p * n);
```

#### shallow Copy

> ES5

```javascript
function shallowCopy(o) {
    var _o = Array.isArray(o) ? [] : {};
    for (var key in o) {
        if (o.hasOwnProperty(key)) {
            _o[key] = o[key];
        }
    }
    return _o;
}
```

> ES6

```javascript
const shallowCopy = o => {
    const _o = Array.isArray(o) ? [] : {};
    for (const key in o) {
        if (o.hasOwnProperty(key)) {
            _o[key] = o[key];
        }
    }
    return _o;
}
```

#### deep Clone

> ES5

```javascript
function deepClone(o) {
    var _o = Array.isArray(o) ? [] : {};
    for (var key of Reflect.ownKeys(o)) {
        if (o.hasOwnProperty(key)) {
            if (o[key] && typeof o[key] === 'object') {
                _o[key] = deepClone(o[key]);
            } else {
                _o[key] = o[key];
            }
        }
    }
    return _o;
}
```

> ES6

```javascript
const deepClone = o => {
    const _o = Array.isArray(o) ? [] : {};
    for (const key of Reflect.ownKeys(o)) {
        if (o.hasOwnProperty(key)) {
            if (o[key] && typeof o[key] === 'object') {
                _o[key] = deepClone(o[key]);
            } else {
                _o[key] = o[key];
            }
        }
    }
    return _o;
}
```

#### deep Clone Loop

> ES5

```javascript
function deepClone(o) {
    var weakMap = new WeakMap();

    function deepCloneFunc(o) {
        var _o = Array.isArray(o) ? [] : {};
        var existObj = weakMap.get(o);
        if (existObj) return existObj;
        weakMap.set(o, o);
        for (var key of Reflect.ownKeys(o)) {
            if (o.hasOwnProperty(key)) {
                if (o[key] && typeof o[key] === 'object') {
                    _o[key] = deepCloneFunc(o[key]);
                } else {
                    _o[key] = o[key];
                }
            }
        }
        return _o;
    }

    return deepCloneFunc(o);
}
```

> ES6

```javascript
const deepClone = o => {
    const weakMap = new WeakMap();

    const deepCloneFunc = o => {
        const _o = Array.isArray(o) ? [] : {};
        const existObj = weakMap.get(o);
        if (existObj) return existObj;
        weakMap.set(o, o);
        for (const key of Reflect.ownKeys(o)) {
            if (o.hasOwnProperty(key)) {
                if (o[key] && typeof o[key] === 'object') {
                    _o[key] = deepCloneFunc(o[key]);
                } else {
                    _o[key] = o[key];
                }
            }
        }
        return _o;
    };

    return deepCloneFunc(o);
};
```

#### pick

> ES5

```javascript
function pick(o, property) {
    var weakMap = new WeakMap();

    function deepClone(o) {
        var _o = Array.isArray(o) ? [] : {};
        var existObj = weakMap.get(o);
        if (existObj) return existObj;
        weakMap.set(o, o);
        for (var key of Reflect.ownKeys(o)) {
            if (o.hasOwnProperty(key) && property.includes(key)) {
                if (o[key] && typeof o[key] === 'object') {
                    _o[key] = deepClone(o[key]);
                } else {
                    _o[key] = o[key];
                }
            }
        }
        return _o;
    }

    return deepClone(o);
}
```

> ES6

```javascript
const pick = (o, property) => {
    const weakMap = new WeakMap();
    const deepClone = (o) => {
        const _o = Array.isArray(o) ? [] : {};
        const existObj = weakMap.get(o);
        if (existObj) return existObj;
        weakMap.set(o, o);
        for (const key of Reflect.ownKeys(o)) {
            if (o.hasOwnProperty(key) && property.includes(key)) {
                _o[key] = deepClone(o[key]);
            } else {
                _o[key] = o[key];
            }
        }
        return _o;
    };
    return deepClone(o);
};
```

#### new

> ES5

```javascript
function myNew(fn) {
    if (typeof fn !== 'function') {
        throw new TypeError('The first argument to a function must be a method');
    }
    var obj = {},
        args = Array.prototype.slice.call(arguments, 1),
        result = fn.apply(obj, args),
        isObject = typeof result === 'object' && result !== null,
        isFunction = typeof result === 'function';
    if (isFunction || isObject) {
        return result;
    }

    obj.__proto__ = fn.prototype;
    return obj;
}
```

> ES6

```javascript
const myNew = (fn, ...args) => {
    if (typeof fn !== 'function') {
        throw new TypeError('The first argument to a function must be a method');
    }
    const obj = {},
        result = fn.call(obj, ...args),
        isObject = typeof result === 'object' && result !== null,
        isFunction = typeof result === 'function';
    if (isObject || isFunction) {
        return result;
    }
    obj.__proto__ = fn.prototype;
    return obj;
};
```

#### instanceof

> ES5

```javascript
function myInstanceof(targetObj, targetClass) {
    if (!targetObj || !targetClass || !targetObj.__proto__ || !targetClass.prototype) {
        return false;
    }
    var current = targetObj;
    while (current) {
        if (current.__proto__ === targetClass.prototype) {
            return true;
        }
        current = current.__proto__;
    }
    return false;
}
```

> ES6

```javascript
const myInstanceof = (targetObj, targetClass) => {
    if (!targetObj || !targetClass || !targetObj.__proto__ || !targetClass.prototype) {
        return false;
    }
    let current = targetObj;
    while (current) {
        if (current.__proto__ === targetClass.prototype) {
            return true;
        }
        current = current.__proto__;
    }
    return false;
};
```

#### unit

> ES5

```javascript
function unit(desc, fn) {
    try {
        fn();
        console.log(desc + ' -> PASS!');
    } catch (e) {
        console.error(desc + ' -> FAILED:', e);
    }
}

function test(res) {
    return {
        toBe: function (expectRes) {
            if (res !== expectRes) {
                throw new Error('期望值是' + expectRes + ', 但实际上得到的是' + res);
            }
        }
    }
}
```

> ES6

```javascript
const unit = (desc, fn) => {
    try {
        fn();
        console.log(`${desc} -> PASS!`);
    } catch (e) {
        console.error(`${desc} -> FAILED:`, e);
    }
}
const test = res => {
    return {
        toBe(expectRes) {
            if (res !== expectRes) {
                throw new Error(`期望值是${expectRes}，但实际上得到的是${res}`);
            }
        }
    };
}
```

#### call

> ES5

```javascript
Function.prototype.myCall = function () {
    if (typeof this !== 'function') {
        throw new TypeError('call 方法必须被一个函数调用!');
    }
    var args = Array.prototype.slice.call(arguments),
        o = args[0] || window,
        params = args.slice(1) || [],
        symbolFunc = Symbol.for('func');
    o[symbolFunc] = this;
    const res = o[symbolFunc](...params);
    delete o[symbolFunc];
    return res;
};
```

> ES6

```javascript
Function.prototype.myCall = function (o = window, ...args) {
    if (typeof this !== 'function') {
        throw new TypeError('call 方法必须被一个函数调用!');
    }
    const symbolFunc = Symbol.for('func');
    o[symbolFunc] = this;
    const res = o[symbolFunc](...args);
    delete o[symbolFunc];
    return res;
};
```

#### apply

> ES5

```javascript
Function.prototype.myApply = function () {
    if (typeof this !== 'function') {
        throw new TypeError('apply 方法必须被一个函数调用!');
    }
    var args = Array.prototype.slice.call(arguments),
        o = args[0] || window,
        params = args[1] || [],
        symbolFunc = Symbol.for('func');
    o[symbolFunc] = this;
    const res = o[symbolFunc](...params);
    delete o[symbolFunc];
    return res;
};
```

> ES6

```javascript
Function.prototype.myApply = function (o = window, args = []) {
    if (typeof this !== 'function') {
        throw new TypeError('apply 方法必须被一个函数调用!');
    }
    const symbolFunc = Symbol.for('func');
    o[symbolFunc] = this;
    const res = o[symbolFunc](...args);
    delete o[symbolFunc];
    return res;
};
```

#### bind

> ES5

```javascript
Function.prototype.myBind = function (context) {
    if (typeof this !== 'function') {
        throw new TypeError('bind 方法必须被一个函数调用!');
    }
    var args = Array.prototype.slice.call(arguments, 1),
        self = this,
        fBind,
        F = function () {
        };
    fBind = function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fBind ? this : context, args.concat(innerArgs));
    };
    F.prototype = self.prototype;
    Object.setPrototypeOf(fBind.prototype, F.prototype);
    return fBind;
};
```

> ES6

```javascript
Function.prototype.myBind = function (context, ...args) {
    if (typeof this !== 'function') {
        throw new TypeError('bind 方法必须被一个函数调用!');
    }
    const self = this,
        F = function () {
        };
    let fBind;
    fBind = function (...innerArgs) {
        return self.apply(this instanceof fBind ? this : context, [...args, ...innerArgs]);
    };
    F.prototype = self.prototype;
    Object.setPrototypeOf(fBind.prototype, F.prototype);
    return fBind;
};
```

#### softBind

> ES5

```javascript
Function.prototype.softBind = function (context) {
    if (typeof this !== 'function') {
        throw new TypeError('softBind 方法必须被一个函数调用!');
    }
    var args = Array.prototype.slice.call(arguments, 1),
        self = this,
        fBind,
        F = function () {
        };
    fBind = function () {
        var innerArgs = Array.prototype.slice.call(arguments);
        return self.apply((this === window || this === undefined) ? context : this, args.concat(innerArgs));
    };
    F.prototype = self.prototype;
    Object.setPrototypeOf(fBind.prototype, F.prototype);
    return fBind;
};
```

> ES6

```javascript
Function.prototype.softBind = function (context, ...args) {
    if (typeof this !== 'function') {
        throw new TypeError('softBind 方法必须被一个函数调用!');
    }
    const self = this,
        F = function () {
        };
    let fBind;
    fBind = function (...innerArgs) {
        return self.apply((this === window || this === undefined) ? context : this, [...args, ...innerArgs]);
    };
    F.prototype = self.prototype;
    Object.setPrototypeOf(fBind.prototype, F.prototype);
    return fBind;
};
```

#### Event Loop

> browser

浏览器中的 Event Loop 其实比较简单,按照流程的步骤如下:

- 首先 JS 线程会区分异/同步程序,遇到同步程序优先执行,遇到异步程序会将其移入至相关的处理线程(定时器线程、异步 HTTP
  请求线程)
- 接着,会将相关的处理线程符合条件的回调事件移入至事件触发线程内,进入 Event Loop 事件循环执行机制
- Event Loop 会等待至同步程序全部执行完成后,轮询事件队列中的回调事件,触发执行
- Event Loop 中的事件队列分为两种: 宏事件、微事件,在每次触发宏事件之前,都会先去轮询执行微事件
    - 宏事件
        - script
        - I/O
        - setTimeout、setInterval
        - setImmediate(Node.js)
        - UI 事件
        - postMessage
    - 微事件
        - Promise
        - process.nextTick(Node.js)
        - Object.observe
        - MutationObserver

> nodejs

nodejs 中的 Event Loop 就有所不同, 其运行的阶段分为:

- timer: 执行 setTimeout、setInterval 回调事件
- pending callbacks: 执行延迟到下一个 I/O 回调
- idle,prepare: 仅系统内部使用
- poll: 检索新的 I/O 事件,执行与 I/O 相关的回调
- check: 执行 setImmediate 回调
- close callbacks: 执行一些关闭的回调函数,如 socket.close

注意: poll 队列执行完成后,如果没有 setImmediate,但是有定时器到期,会绕回执行定时器 timer 阶段

#### Promise

> ES5

```javascript
var Promise = (function () {
    var PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    function Promise(fn) {
        this.status = PENDING;
        this.value = null;
        this.reason = null;
        this.onFulfilledCallbacks = [];
        this.onRejectedCallbacks = [];

        var that = this;

        function resolve(value) {
            if (that.status === PENDING) {
                that.status = FULFILLED;
                that.value = value;
                that.onFulfilledCallbacks.forEach(function (callback) {
                    callback(that.value);
                });
            }
        }

        function reject(reason) {
            if (that.status === PENDING) {
                that.status = REJECTED;
                that.reason = reason;
                that.onRejectedCallbacks.forEach(function (callback) {
                    callback(that.reason);
                });
            }
        }

        try {
            fn(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    Promise.prototype.then = function (onFulfilled, onRejected) {
        if (typeof onFulfilled !== 'function') {
            onFulfilled = function (value) {
                return value;
            };
        }
        if (typeof onRejected !== 'function') {
            onRejected = function (reason) {
                if (reason instanceof Error) {
                    throw reason;
                } else {
                    throw new Error(reason);
                }
            };
        }
        let promise,
            then,
            that = this;
        if (this.status === FULFILLED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    (function thenable(value) {
                        if (typeof onFulfilled !== 'function') {
                            resolve(value);
                        } else {
                            then = value.then;
                            if (typeof then === 'function') {
                                then.call(that, function (y) {
                                    thenable.call(that, y);
                                }, function (r) {
                                    reject(r);
                                });
                            } else {
                                const x = onFulfilled(value);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        }
                    })(that.value);
                }, 0);
            });
        }
        if (this.status === REJECTED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    try {
                        if (typeof onRejected !== 'function') {
                            reject(that.reason);
                        } else {
                            const x = onRejected(that.reason);
                            resolvePromise(promise, x, resolve, reject);
                        }
                    } catch (error) {
                        reject(error);
                    }
                }, 0);
            });
        }
        if (this.status === PENDING) {
            promise = new Promise(function (resolve, reject) {
                that.onFulfilledCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            (function thenable(value) {
                                if (typeof onFulfilled !== 'function') {
                                    resolve(value);
                                } else {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(that, function (y) {
                                            thenable.call(that, y);
                                        }, function (r) {
                                            reject(r);
                                        });
                                    } else {
                                        const x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                }
                            })(that.value);
                        } catch (error) {
                            reject(error);
                        }
                    }, 0);
                });
                that.onRejectedCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(that.reason);
                            } else {
                                const x = onRejected(that.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (error) {
                            reject(error);
                        }
                    }, 0);
                });
            });
        }

        return promise
    };

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new TypeError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            x.then(y => {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            var then,
                call = false;
            try {
                then = x.then;
            } catch (error) {
                reject(error);
            }
            if (typeof then === 'function') {
                try {
                    then.call(x, function (y) {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, function (r) {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (error) {
                    if (call) return;
                    reject(error);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    return Promise;
})();
```

> ES6

```javascript
const Promise = (() => {
    const PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    class Promise {
        constructor(fn) {
            this.status = PENDING;
            this.value = null;
            this.reason = null;
            this.onFulfilledCallbacks = [];
            this.onRejectedCallbacks = [];

            const resolve = (value) => {
                if (this.status === PENDING) {
                    this.status = FULFILLED;
                    this.value = value;
                    this.onFulfilledCallbacks.forEach(callback => {
                        callback(this.value);
                    });
                }
            };

            const reject = (reason) => {
                if (this.status === PENDING) {
                    this.status = REJECTED;
                    this.reason = reason;
                    this.onRejectedCallbacks.forEach(callback => {
                        callback(this.reason);
                    });
                }
            };

            try {
                fn(resolve, reject);
            } catch (err) {
                reject(err);
            }
        }

        then(onFulfilled, onRejected) {
            if (typeof onFulfilled !== 'function') {
                onFulfilled = value => value;
            }
            if (typeof onRejected !== 'function') {
                onRejected = reason => {
                    if (reason instanceof Error) {
                        throw reason
                    } else {
                        throw new Error(reason);
                    }
                }
            }
            let promise,
                then;
            if (this.status === FULFILLED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            (function thenable(value) {
                                if (typeof onFulfilled !== 'function') {
                                    resolve(value);
                                } else {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(this, y => {
                                            thenable.call(this, y);
                                        }, r => {
                                            reject(r);
                                        });
                                    } else {
                                        const x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                }
                            }.bind(this))(this.value);
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === REJECTED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(this.reason);
                            } else {
                                const x = onRejected(this.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === PENDING) {
                promise = new Promise((resolve, reject) => {
                    this.onFulfilledCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                (function thenable(value) {
                                    if (typeof onFulfilled !== 'function') {
                                        resolve(value);
                                    } else {
                                        then = value.then;
                                        if (typeof then === 'function') {
                                            then.call(this, y => {
                                                thenable.call(this, y);
                                            }, r => {
                                                reject(r);
                                            });
                                        } else {
                                            const x = onFulfilled(value);
                                            resolvePromise(promise, x, resolve, reject);
                                        }
                                    }
                                }.bind(this))(this.value);
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                    this.onRejectedCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                if (typeof onRejected !== 'function') {
                                    reject(this.reason);
                                } else {
                                    const x = onRejected(this.reason);
                                    resolvePromise(promise, x, resolve, reject);
                                }
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                });
            }
            return promise;
        }
    }

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new TypeError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            x.then(y => {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            let then,
                call = false;

            try {
                then = x.then;
            } catch (err) {
                reject(err);
            }

            if (typeof then === 'function') {
                try {
                    then.call(x, y => {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, r => {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (err) {
                    if (call) return;
                    reject(err);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    return Promise;
})();
```

#### complete Promise

> ES5

```javascript
var Promise = (function () {
    var PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    function Promise(fn) {
        this.status = PENDING;
        this.value = null;
        this.reason = null;
        this.onFulfilledCallbacks = [];
        this.onRejectedCallbacks = [];

        var that = this;

        function resolve(value) {
            if (that.status === PENDING) {
                that.status = FULFILLED;
                that.value = value;
                that.onFulfilledCallbacks.forEach(function (callback) {
                    callback(that.value);
                });
            }
        }

        function reject(reason) {
            if (that.status === PENDING) {
                that.status = REJECTED;
                that.reason = reason;
                that.onRejectedCallbacks.forEach(function (callback) {
                    callback(that.reason);
                });
            }
        }

        try {
            fn(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    Promise.prototype.then = function (onFulfilled, onRejected) {
        if (typeof onFulfiled !== 'function') {
            onFulfilled = function (value) {
                return value;
            };
        }
        if (typeof onRejected !== 'function') {
            onRejected = function (reason) {
                if (reason instanceof Error) {
                    throw reason;
                } else {
                    throw new Error(reason);
                }
            };
        }
        var promise,
            then,
            that = this;
        if (this.status === FULFILLED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    try {
                        if (typeof onFulfilled !== 'function') {
                            resolve(that.value);
                        } else {
                            (function thenable(value) {
                                then = value.then;
                                if (typeof then === 'function') {
                                    then.call(that, function (y) {
                                        thenable.call(that, y);
                                    }, function (r) {
                                        reject(r);
                                    });
                                } else {
                                    var x = onFulfilled(value);
                                    resolvePromise(promise, x, resolve, reject);
                                }
                            })(that.value);
                        }
                    } catch (err) {
                        reject(err);
                    }
                }, 0);
            });
        }
        if (this.status === REJECTED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    try {
                        if (typeof onRejected !== 'function') {
                            reject(that.reason);
                        } else {
                            var x = onRejected(that.reason);
                            resolvePromise(promise, x, resolve, reject);
                        }
                    } catch (err) {
                        reject(err);
                    }
                }, 0);
            });
        }
        if (this.status === PENDING) {
            promise = new Promise(function (resolve, reject) {
                that.onFulfilledCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            if (typeof onFulfilled !== 'function') {
                                resolve(that.value);
                            } else {
                                (function thenable(value) {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(that, function (y) {
                                            thenable.call(that, y);
                                        }, function (r) {
                                            reject(r);
                                        });
                                    } else {
                                        var x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                })(that.value);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
                that.onRejectedCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(that.reason);
                            } else {
                                var x = onRejected(that.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            });
        }
        return promise;
    };

    Promise.prototype.catch = function (onRejected) {
        return this.then(null, onRejected);
    };

    Promise.prototype.finally = function (fn) {
        if (typeof fn !== 'function') {
            throw new TypeError('parameter must be a function');
        }
        return this.then(function _resolve(value) {
            return Promise.resolve(fn()).then(function () {
                return value;
            });
        }, function _reject(reason) {
            return Promise.resolve(fn()).then(function () {
                throw reason;
            });
        });
    };

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new TypeError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            return x.then(function (y) {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            var then,
                call = false;
            try {
                then = x.then;
            } catch (err) {
                reject(err);
            }
            if (typeof then === 'function') {
                try {
                    then.call(x, function (y) {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, function (r) {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (err) {
                    if (call) return;
                    reject(err);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    Promise.resolve = function (params) {
        if (params instanceof Promise) {
            return params;
        }
        return new Promise(function (resolve) {
            resolve(params);
        });
    };

    Promise.reject = function (reason) {
        return new Promise(function (resolve, reject) {
            reject(reason);
        });
    };

    Promise.all = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            var count = 0,
                result = [],
                length = promiseLists.length;
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    result[count++] = value;
                    if (count === length) {
                        resolve(result);
                    }
                }, function _reject(reason) {
                    reject(reason);
                });
            });
        });
    };

    Promise.race = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    resolve(value);
                }, function _reject(reason) {
                    reject(reason);
                });
            });
        });
    };

    Promise.allSettled = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            var count = 0,
                result = [],
                length = promiseLists.length;
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    result[count++] = {
                        status: 'fulfilled',
                        value
                    };
                    if (count === length) {
                        resolve(result);
                    }
                }, function _reject(reason) {
                    result[count++] = {
                        status: 'rejected',
                        reason
                    };
                    if (count === length) {
                        resolve(result);
                    }
                });
            });
        });
    };

    return Promise;
})();
```

> ES6

```javascript
const Promise = (() => {
    const PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    class Promise {
        constructor(fn) {
            this.status = PENDING;
            this.value = null;
            this.reason = null;
            this.onFulfilledCallbacks = [];
            this.onRejectedCallbacks = [];

            const resolve = (value) => {
                if (this.status === PENDING) {
                    this.status = FULFILLED;
                    this.value = value;
                    this.onFulfilledCallbacks.forEach(callback => {
                        callback(this.value);
                    });
                }
            };

            const reject = (reason) => {
                if (this.status === PENDING) {
                    this.status = REJECTED;
                    this.reason = reason;
                    this.onRejectedCallbacks.forEach(callback => {
                        callback(this.reason);
                    });
                }
            };

            try {
                fn(resolve, reject);
            } catch (err) {
                reject(err);
            }
        }

        then(onFulfilled, onRejected) {
            if (typeof onFulfilled !== 'function') {
                onFulfilled = value => value;
            }
            if (typeof onRejected !== 'function') {
                onRejected = reason => {
                    if (reason instanceof Error) {
                        throw reason;
                    } else {
                        throw new Error(reason);
                    }
                };
            }
            let promise,
                then;
            if (this.status === FULFILLED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            if (typeof onFulfilled !== 'function') {
                                resolve(this.value);
                            } else {
                                (function thenable(value) {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(this, y => {
                                            thenable.call(this, y);
                                        }, r => {
                                            reject(r);
                                        });
                                    } else {
                                        const x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                }.bind(this))(this.value);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === REJECTED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(this.reason);
                            } else {
                                const x = onRejected(this.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === PENDING) {
                promise = new Promise((resolve, reject) => {
                    this.onFulfilledCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                if (typeof onFulfilled !== 'function') {
                                    resolve(this.value);
                                } else {
                                    (function thenable(value) {
                                        then = value.then;
                                        if (typeof then === 'function') {
                                            then.call(this, y => {
                                                thenable.call(this, y);
                                            }, r => {
                                                reject(r);
                                            });
                                        } else {
                                            const x = onFulfilled(value);
                                            resolvePromise(promise, x, resolve, reject);
                                        }
                                    }.bind(this))(this.value);
                                }
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                    this.onRejectedCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                if (typeof onRejected !== 'function') {
                                    reject(this.reason);
                                } else {
                                    const x = onRejected(this.reason);
                                    resolvePromise(promise, x, resolve, reject);
                                }
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                });
            }
            return promise;
        }

        catch(onRejected) {
            return this.then(null, onRejected);
        }

        finally(fn) {
            if (typeof fn !== 'function') {
                throw new TypeError('parameter must be a function');
            }
            return this.then(value => {
                return Promise.resolve(fn()).then(() => value);
            }, reason => {
                return Promise.resolve(fn()).then(() => {
                    throw reason;
                });
            });
        }
    }

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new ReferenceError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            return x.then(y => {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            let then,
                call = false;
            try {
                then = x.then;
            } catch (err) {
                reject(err);
            }
            if (typeof then === 'function') {
                try {
                    then.call(x, y => {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, r => {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (err) {
                    if (call) return;
                    reject(err);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    Promise.resolve = params => {
        if (params instanceof Promise) {
            return params;
        }
        return new Promise(resolve => {
            resolve(params);
        });
    };

    Promise.reject = reason => {
        return new Promise((resolve, reject) => {
            reject(reason);
        });
    };

    Promise.all = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            let count = 0;
            const length = promiseLists.length,
                result = [];
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    result[count++] = value;
                    if (count === length) {
                        resolve(result);
                    }
                }, reason => {
                    reject(reason);
                });
            });
        });
    };

    Promise.race = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    resolve(value);
                }, reason => {
                    reject(reason);
                });
            });
        });
    };

    Promise.allSettled = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            let count = 0;
            const length = promiseLists.length,
                result = [];
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    result[count++] = {
                        status: 'fulfilled',
                        value
                    };
                    if (count === length) {
                        resolve(result);
                    }
                    resolve(result);
                }, reason => {
                    result[count++] = {
                        status: 'rejected',
                        reason
                    };
                    if (count === length) {
                        resolve(result);
                    }
                });
            });
        });
    };

    return Promise;
})();
```

#### complete Promise Test

> ES5

```javascript
var Promise = (function () {
    var PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    function Promise(fn) {
        this.status = PENDING;
        this.value = null;
        this.reason = null;
        this.onFulfilledCallbacks = [];
        this.onRejectedCallbacks = [];

        var that = this;

        function resolve(value) {
            if (that.status === PENDING) {
                that.status = FULFILLED;
                that.value = value;
                that.onFulfilledCallbacks.forEach(function (callback) {
                    callback(that.value);
                });
            }
        }

        function reject(reason) {
            if (that.status === PENDING) {
                that.status = REJECTED;
                that.reason = reason;
                that.onRejectedCallbacks.forEach(function (callback) {
                    callback(that.reason);
                });
            }
        }

        try {
            fn(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    Promise.prototype.then = function (onFulfilled, onRejected) {
        if (typeof onFulfiled !== 'function') {
            onFulfilled = function (value) {
                return value;
            };
        }
        if (typeof onRejected !== 'function') {
            onRejected = function (reason) {
                if (reason instanceof Error) {
                    throw reason;
                } else {
                    throw new Error(reason);
                }
            };
        }
        var promise,
            then,
            that = this;
        if (this.status === FULFILLED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    try {
                        if (typeof onFulfilled !== 'function') {
                            resolve(that.value);
                        } else {
                            (function thenable(value) {
                                then = value.then;
                                if (typeof then === 'function') {
                                    then.call(that, function (y) {
                                        thenable.call(that, y);
                                    }, function (r) {
                                        reject(r);
                                    });
                                } else {
                                    var x = onFulfilled(value);
                                    resolvePromise(promise, x, resolve, reject);
                                }
                            })(that.value);
                        }
                    } catch (err) {
                        reject(err);
                    }
                }, 0);
            });
        }
        if (this.status === REJECTED) {
            promise = new Promise(function (resolve, reject) {
                setTimeout(function () {
                    try {
                        if (typeof onRejected !== 'function') {
                            reject(that.reason);
                        } else {
                            var x = onRejected(that.reason);
                            resolvePromise(promise, x, resolve, reject);
                        }
                    } catch (err) {
                        reject(err);
                    }
                }, 0);
            });
        }
        if (this.status === PENDING) {
            promise = new Promise(function (resolve, reject) {
                that.onFulfilledCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            if (typeof onFulfilled !== 'function') {
                                resolve(that.value);
                            } else {
                                (function thenable(value) {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(that, function (y) {
                                            thenable.call(that, y);
                                        }, function (r) {
                                            reject(r);
                                        });
                                    } else {
                                        var x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                })(that.value);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
                that.onRejectedCallbacks.push(function () {
                    setTimeout(function () {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(that.reason);
                            } else {
                                var x = onRejected(that.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            });
        }
        return promise;
    };

    Promise.prototype.catch = function (onRejected) {
        return this.then(null, onRejected);
    };

    Promise.prototype.finally = function (fn) {
        if (typeof fn !== 'function') {
            throw new TypeError('parameter must be a function');
        }
        return this.then(function _resolve(value) {
            return Promise.resolve(fn()).then(function () {
                return value;
            });
        }, function _reject(reason) {
            return Promise.resolve(fn()).then(function () {
                throw reason;
            });
        });
    };

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new TypeError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            return x.then(function (y) {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            var then,
                call = false;
            try {
                then = x.then;
            } catch (err) {
                reject(err);
            }
            if (typeof then === 'function') {
                try {
                    then.call(x, function (y) {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, function (r) {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (err) {
                    if (call) return;
                    reject(err);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    Promise.resolve = function (params) {
        if (params instanceof Promise) {
            return params;
        }
        return new Promise(function (resolve) {
            resolve(params);
        });
    };

    Promise.reject = function (reason) {
        return new Promise(function (resolve, reject) {
            reject(reason);
        });
    };

    Promise.all = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            var count = 0,
                result = [],
                length = promiseLists.length;
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    result[count++] = value;
                    if (count === length) {
                        resolve(result);
                    }
                }, function _reject(reason) {
                    reject(reason);
                });
            });
        });
    };

    Promise.race = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    resolve(value);
                }, function _reject(reason) {
                    reject(reason);
                });
            });
        });
    };

    Promise.allSettled = function (promiseLists) {
        return new Promise(function (resolve, reject) {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            var count = 0,
                result = [],
                length = promiseLists.length;
            promiseLists.forEach(function (promise) {
                Promise.resolve(promise).then(function _resolve(value) {
                    result[count++] = {
                        status: 'fulfilled',
                        value
                    };
                    if (count === length) {
                        resolve(result);
                    }
                }, function _reject(reason) {
                    result[count++] = {
                        status: 'rejected',
                        reason
                    };
                    if (count === length) {
                        resolve(result);
                    }
                });
            });
        });
    };

    Promise.deferred = function () {
        const dfd = {};
        dfd.promise = new Promise((resolve, reject) => {
            dfd.resolve = resolve;
            dfd.reject = reject;
        });
        return dfd;
    };

    return Promise;
})();
module.exports = Promise;
```

> ES6

```javascript
const Promise = (() => {
    const PENDING = 'PENDING',
        FULFILLED = 'FULFILLED',
        REJECTED = 'REJECTED';

    class Promise {
        constructor(fn) {
            this.status = PENDING;
            this.value = null;
            this.reason = null;
            this.onFulfilledCallbacks = [];
            this.onRejectedCallbacks = [];

            const resolve = (value) => {
                if (this.status === PENDING) {
                    this.status = FULFILLED;
                    this.value = value;
                    this.onFulfilledCallbacks.forEach(callback => {
                        callback(this.value);
                    });
                }
            };

            const reject = (reason) => {
                if (this.status === PENDING) {
                    this.status = REJECTED;
                    this.reason = reason;
                    this.onRejectedCallbacks.forEach(callback => {
                        callback(this.reason);
                    });
                }
            };

            try {
                fn(resolve, reject);
            } catch (err) {
                reject(err);
            }
        }

        then(onFulfilled, onRejected) {
            if (typeof onFulfilled !== 'function') {
                onFulfilled = value => value;
            }
            if (typeof onRejected !== 'function') {
                onRejected = reason => {
                    if (reason instanceof Error) {
                        throw reason;
                    } else {
                        throw new Error(reason);
                    }
                };
            }
            let promise,
                then;
            if (this.status === FULFILLED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            if (typeof onFulfilled !== 'function') {
                                resolve(this.value);
                            } else {
                                (function thenable(value) {
                                    then = value.then;
                                    if (typeof then === 'function') {
                                        then.call(this, y => {
                                            thenable.call(this, y);
                                        }, r => {
                                            reject(r);
                                        });
                                    } else {
                                        const x = onFulfilled(value);
                                        resolvePromise(promise, x, resolve, reject);
                                    }
                                }.bind(this))(this.value);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === REJECTED) {
                promise = new Promise((resolve, reject) => {
                    setTimeout(() => {
                        try {
                            if (typeof onRejected !== 'function') {
                                reject(this.reason);
                            } else {
                                const x = onRejected(this.reason);
                                resolvePromise(promise, x, resolve, reject);
                            }
                        } catch (err) {
                            reject(err);
                        }
                    }, 0);
                });
            }
            if (this.status === PENDING) {
                promise = new Promise((resolve, reject) => {
                    this.onFulfilledCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                if (typeof onFulfilled !== 'function') {
                                    resolve(this.value);
                                } else {
                                    (function thenable(value) {
                                        then = value.then;
                                        if (typeof then === 'function') {
                                            then.call(this, y => {
                                                thenable.call(this, y);
                                            }, r => {
                                                reject(r);
                                            });
                                        } else {
                                            const x = onFulfilled(value);
                                            resolvePromise(promise, x, resolve, reject);
                                        }
                                    }.bind(this))(this.value);
                                }
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                    this.onRejectedCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                if (typeof onRejected !== 'function') {
                                    reject(this.reason);
                                } else {
                                    const x = onRejected(this.reason);
                                    resolvePromise(promise, x, resolve, reject);
                                }
                            } catch (err) {
                                reject(err);
                            }
                        }, 0);
                    });
                });
            }
            return promise;
        }

        catch(onRejected) {
            return this.then(null, onRejected);
        }

        finally(fn) {
            if (typeof fn !== 'function') {
                throw new TypeError('parameter must be a function');
            }
            return this.then(value => {
                return Promise.resolve(fn()).then(() => value);
            }, reason => {
                return Promise.resolve(fn()).then(() => {
                    throw reason;
                });
            });
        }
    }

    function resolvePromise(promise, x, resolve, reject) {
        if (promise === x) {
            return reject(new ReferenceError('The promise and the return value are the same'));
        }
        if (x instanceof Promise) {
            return x.then(y => {
                resolvePromise(promise, y, resolve, reject);
            });
        }
        if (typeof x === 'object' || typeof x === 'function') {
            if (x === null) {
                return reject(x);
            }
            let then,
                call = false;
            try {
                then = x.then;
            } catch (err) {
                reject(err);
            }
            if (typeof then === 'function') {
                try {
                    then.call(x, y => {
                        if (call) return;
                        call = true;
                        resolvePromise(promise, y, resolve, reject);
                    }, r => {
                        if (call) return;
                        call = true;
                        reject(r);
                    });
                } catch (err) {
                    if (call) return;
                    reject(err);
                }
            } else {
                resolve(x);
            }
        } else {
            resolve(x);
        }
    }

    Promise.resolve = params => {
        if (params instanceof Promise) {
            return params;
        }
        return new Promise(resolve => {
            resolve(params);
        });
    };

    Promise.reject = reason => {
        return new Promise((resolve, reject) => {
            reject(reason);
        });
    };

    Promise.all = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            let count = 0;
            const length = promiseLists.length,
                result = [];
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    result[count++] = value;
                    if (count === length) {
                        resolve(result);
                    }
                }, reason => {
                    reject(reason);
                });
            });
        });
    };

    Promise.race = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    resolve(value);
                }, reason => {
                    reject(reason);
                });
            });
        });
    };

    Promise.allSettled = promiseLists => {
        return new Promise((resolve, reject) => {
            if (!Array.isArray(promiseLists)) {
                reject(new ReferenceError('parameter must be an array'));
            }
            let count = 0;
            const length = promiseLists.length,
                result = [];
            promiseLists.forEach(promise => {
                Promise.resolve(promise).then(value => {
                    result[count++] = {
                        status: 'fulfilled',
                        value
                    };
                    if (count === length) {
                        resolve(result);
                    }
                    resolve(result);
                }, reason => {
                    result[count++] = {
                        status: 'rejected',
                        reason
                    };
                    if (count === length) {
                        resolve(result);
                    }
                });
            });
        });
    };

    Promise.deferred = () => {
        const dfd = {};
        dfd.promise = new Promise((resolve, reject) => {
            dfd.resolve = resolve;
            dfd.reject = reject;
        });
        return dfd;
    };

    return Promise;
})();
module.exports = Promise;
```

#### generator Thunk

> ES5

```javascript
function Thunk(fn) {
    var that = this;
    return function (...args) {
        return function (callback) {
            return fn.call(that, ...args, callback);
        }
    }
}

function run(taskRun) {
    var task = taskRun();
    function next(err, data) {
        if (err) return task.throw(err instanceof Error ? err : new Error(err));
        const result = task.next(data);
        if (result.done) return true;
        if (typeof result.value === 'function') {
            result.value(next);
        } else {
            next(null, result.value);
        }
    }
    next();
}
```

> ES6

```javascript
const Thunk = fn => (...args) => callback => fn.call(this, ...args, callback);
const run = taskRun => {
    const task = taskRun();
    const next = (err, data) => {
        if (err) return task.throw(err instanceof Error ? err : new Error(err));
        const {value, done} = task.next(data);
        if (done) return true;
        if (typeof value === 'function') {
            value(next);
        } else {
            next(null, value);
        }
    }
    next();
};
```

#### promise Generator Thunk

> ES5

```javascript
function Thunk(fn) {
    var that = this;
    return function (...args) {
        return function (callback) {
            return fn.call(that, ...args, callback);
        }
    }
}

function run(taskRun) {
    return new Promise(function (resolve, reject) {
        const task = taskRun();
        function next(err, data) {
            if (err) return reject(task.throw(err instanceof Error ? err : new Error(err)));
            const {value, done} = task.next(data);
            if (done) return resolve(value);
            if (typeof value === 'function') {
                return value(next);
            }
            const promise = Promise.resolve(value);
            promise.then(val => {
                next(null, val);
            }, reason => {
                next(reason);
            });
        }
        next();
    });
}
```

> ES6

```javascript
const Thunk = fn => (...args) => callback => fn(...args, callback);
const run = taskRun => {
    return new Promise((resolve, reject) => {
        const task = taskRun();
        function next(err, data) {
            if (err) return reject(task.throw(err instanceof Error ? err : new Error(err)));
            const {value, done} = task.next(data);
            if (done) return resolve(value);
            if (typeof value === 'function') {
                return value(next);
            }
            const promise = Promise.resolve(value);
            promise.then(val => {
                next(null, val);
            }, reason => {
                next(reason);
            });
        }
        next();
    });
};
```

#### promise Timeout

> ES5

```javascript
function timeoutPromise(promise, timeout) {
    return Promise.race([Promise.resolve(promise).then(function (value) {
        return (typeof value !== 'object' || !value.hasOwnProperty('status')) ? {
            status: 'fulfilled',
            value
        } : value
    }), new Promise(function (resolve, rejected) {
        setTimeout(function () {
            rejected({
                status: 'rejected',
                reason: '请求超时!'
            });
        }, timeout);
    })]);
}
```

> ES6

```javascript
const timerPromise = (promise, timeout) => Promise.race([Promise.resolve(promise).then(value =>
    (typeof value !== 'object' || !value.hasOwnProperty('status')) ? {
        status: 'fulfilled',
        value
    } : value), new Promise((resolve, rejected) => {
    setTimeout(() => {
        rejected({
            status: 'rejected',
            reason: '请求超时!'
        });
    }, timeout);
})]);
```

#### get Character's Length

> ES5

```javascript
function getLength(str) {
    const strTransform = str.toString() || String(str);
    return strTransform.match(/[\s\S]/ug).length;
}
```

> ES6

```javascript
const getLength = str => (str.toString() || String(str)).match(/[\s\S]/ug).length;
```

#### judge UTF-16 Character

> ES5

```javascript
function isUTF16(str) {
    const strTransform = str.toString() || String(str);
    return strTransform.codePointAt(0) > 0xFFFF;
}
```

> ES6

```javascript
const isUTF16 = str => (str.toString() || String(str)).codePointAt(0) > 0xFFFF;
```

#### flags Method Imitate

> ES5

```javascript
function flagsImitate(regExp) {
    if (!regExp instanceof RegExp)
        throw new TypeError('parameter mast be a RegExp');
    var regExpStr = regExp.toString() || String(regExp);
    return regExpStr.substring(regExpStr.lastIndexOf('/') + 1);
}
```

> ES6

```javascript
const flagsImitate = (regExp) => {
    if (!regExp instanceof RegExp)
        throw new TypeError('parameter mast be a RegExp');
    const regExpStr = regExp.toString() || String(regExp);
    return regExpStr.substring(regExpStr.lastIndexOf('/') + 1);
}
```

#### class imitate

> ES5

```javascript
let classImitate = (function () {
    const classImitate = function (...args) {
        if (new.target === undefined) {
            throw new TypeError('This class can only be called by the new constructor');
        }
        this.args = args;
    };
    Object.defineProperty(classImitate.prototype, 'method', {
        value: function () {
            if (new.target === undefined) {
                throw new TypeError('This method cannot be called by the new constructor');
            }
            console.log('Contains class parameters:', args);
        },
        enumerable: false
    });
    Object.defineProperty(classImitate.prototype, 'getter', {
        set: function () {
            return this.args.join(',');
        },
        enumerable: false
    });
})();
```

> ES6

```javascript
let classImitate = (() => {
    const classImitate = function (...args) {
        if (new.target === undefined) {
            throw new TypeError('This class can only be called by the new constructor');
        }
        this.args = args;
    };
    Object.defineProperty(classImitate.prototype, 'method', {
        value() {
            if (new.target !== undefined) {
                throw new TypeError('This method cannot be called by the new constructor');
            }
            console.log('Contains class parameters:', args);
        },
        enumerable: false
    });
    Object.defineProperty(classImitate.prototype, 'getter', {
        get() {
            return this.args.join(',');
        },
        enumerable: false
    });
})();
```

#### class with array attributes

> ES5

```javascript
function toUnit32(key) {
    return Math.floor(Math.abs(Number(key))) % Math.pow(2, 32);
}

function isArrayIndex(key) {
    const numberRicKey = toUnit32(key);
    return String(numberRicKey) === key && numberRicKey < Math.pow(2, 32) - 1;
}

function MyArray(...args) {
    Array.call(this, ...args);
    return new Proxy(this, {
        set(target, key, value) {
            const length = Reflect.get(target, 'length');
            if (isArrayIndex(key)) {
                if (key >= length) {
                    Reflect.set(target, 'length', key + 1);
                }
            } else {
                if (key < length) {
                    for (let i = length; i >= key; i--) {
                        Reflect.deleteProperty(target, i);
                    }
                }
            }
            return Reflect.set(target, key, value);
        }
    });
}

Object.setPrototypeOf(MyArray.prototype, Array.prototype);
```

> ES6

```javascript
class MyArray extends Array {
    constructor(...args) {
        super(...args);
        return new Proxy(this, {
            set(target, key, value) {
                const length = Reflect.get(target, 'length');
                if (target.isArrayIndex(key)) {
                    if (key >= length) {
                        Reflect.set(target, 'length', key + 1);
                    }
                } else {
                    if (key < length) {
                        for (let i = length; i >= key; i--) {
                            Reflect.deleteProperty(target, i);
                        }
                    }
                }
                return Reflect.set(target, key, value);
            }
        });
    }

    isArrayIndex(key) {
        const numberRic = this.toUnit32(key);
        return String(numberRic) === key && numberRic < MyArray.pow(2, 32) - 1;
    }

    toUnit32(key) {
        return Math.floor(Math.abs(Number(key))) % Math.pow(2, 32);
    }
}
```

#### dateFormat ago

> ES5

```javascript
Date.prototype.__defineGetter__('ago', function () {
    var differ = (new Date().getTime() - this.getTime()) / 1000,
        day_differ = Math.floor(differ / 86400);
    return !day_differ && (differ < 60 && 'Just now' ||
        differ < 120 && '1 minute ago' ||
        differ < 3600 && Math.floor(differ / 60) + ' minutes ago' ||
        differ < 7200 && '1 hour ago' ||
        differ < 86400 && Math.floor(differ / 3600) + ' hours ago'
    ) || (day_differ === 1 && 'Yesterday' ||
        day_differ < 7 && day_differ + ' days ago' ||
        Math.ceil(day_differ / 7) + ' weeks ago');
});
```

> ES6

```javascript
Date.prototype.__defineGetter__('ago', function (){
    const differ = (new Date().getTime() - this.getTime()) / 1000,
        day_differ = Math.floor(differ / 86400);
    return !day_differ &&
        (differ < 60 && 'Just now' ||
            differ < 120 && '1 minute ago' ||
            differ < 3600 && `${Math.floor(differ / 60)} minutes ago` ||
            differ < 7200 && '1 hour ago' ||
            differ < 86400 && `${Math.floor(differ / 3600)} hours ago`) ||
        (day_differ === 1 && 'Yesterday' ||
            day_differ < 7 && `${day_differ} days ago` ||
           `${Math.ceil(day_differ / 7)} weeks ago`);
});
```

#### resolvePath CLI

> ES5

```javascript
var fs = require('fs');
require('colors');

function resolvePathCli() {
    var map = new Map();
    console.log('');
    console.log('当前路径下的文件/目录列表如下:');
    fs.readdir(process.cwd() + '/', function (err, files) {
        error(err);
        console.log('');
        var length = files.length;

        function filesOrDirFunc(i = 0) {
            var filesOrDir = files[i];
            fs.stat(process.cwd() + '/' + filesOrDir, function (err, stats) {
                var statsResult = '';
                map.set(i, stats);
                if (stats.isDirectory()) {
                    statsResult = '        ' + i + ': ' + filesOrDir + '/';
                    console.log(statsResult.blue);
                } else {
                    statsResult = '        ' + i + ': ' + filesOrDir;
                    console.log(statsResult.cyan);
                }
                if (++i === length) {
                    read();
                } else {
                    filesOrDirFunc(i);
                }
            });
        }

        function read() {
            console.log('');
            process.stdout.write('请您选择当前路径下的文件/目录序号: ');
            process.stdin.on('data', options);
            process.stdin.setEncoding('utf-8');
        }

        function options(number) {
            console.log('');
            number = Number(number);
            if (!files[number]) {
                throw new TypeError('必须输入的是文件/目录序号!');
            } else {
                const filesOrDirStats = map.get(number);
                if (filesOrDirStats.isDirectory()) {
                    fs.readdir(process.cwd() + '/' + files[number], 'utf-8', function (err, files) {
                        error(err);
                        const filesLength = files.length;
                        console.log('        共有 ' + filesLength + ' 件文件');
                        console.log('');
                        files.forEach(function file(item) {
                            console.log('            - ' + item);
                        });
                        console.log('');
                        process.stdin.pause();
                    });
                } else {
                    fs.readFile(process.cwd() + '/' + files[number], 'utf-8', function (err, data) {
                        error(err);
                        console.log(data.replace(/(.*)/g, '        $1'));
                        console.log('');
                        process.stdin.pause();
                    });
                }
            }
        }

        filesOrDirFunc();
    });

    function error(err) {
        if (err) {
            if (err instanceof Error)
                throw err;
            else
                throw new Error(err);
        }
    }
}
```

> ES6
```javascript
const fs = require('fs');
require('colors');

const resolvePathCli = () => {
    const map = new Map();
    console.log('');
    console.log('当前路径下的文件/目录列表如下:');
    fs.readdir(`${process.cwd()}/`, (err, files) => {
        error(err);

        console.log('');
        const length = files.length;

        const filesOrDirFunc = (i = 0) => {
            const filesOrDir = files[i];
            fs.stat(`${process.cwd()}/${filesOrDir}`, (err, stats) => {
                error(err);
                map.set(i, stats);
                if (stats.isDirectory()) {
                    console.log(`        ${i}: ${filesOrDir}/`.blue);
                } else {
                    console.log(`        ${i}: ${filesOrDir}`.cyan);
                }

                if (++i === length) {
                    read();
                } else {
                    filesOrDirFunc(i);
                }
            });
        };

        const read = () => {
            console.log('');
            process.stdout.write('请您选择当前路径下的文件/目录序号: ');
            process.stdin.on('data', options);
            process.stdin.setEncoding('utf-8');
        };

        const options = (number) => {
            console.log('');
            number = Number(number);
            const filesDir = files[number];
            if (!filesDir) {
                throw new TypeError('');
            } else {
                const stats = map.get(number);
                if (stats.isDirectory()) {
                    fs.readdir(`${process.cwd()}/${filesDir}`, 'utf-8', (err, files) => {
                        error(err);
                        const filesLength = files.length;
                        console.log(`        共有 ${filesLength} 件文件:`);
                        console.log('');
                        files.forEach(item => {
                            console.log(`            - ${item}`);
                        });
                        console.log('');
                        process.stdin.pause();
                    });
                } else {
                    fs.readFile(`${process.cwd()}/${filesDir}`, 'utf-8', (err, data) => {
                        error(err);
                        console.log(data.replace(/(.*)/g, '        $1'));
                        console.log('');
                        process.stdin.pause();
                    });
                }
            }
        };

        filesOrDirFunc();
    });

    function error(err) {
        if (err) {
            if (err instanceof Error)
                throw err;
            else
                throw new Error(err);
        }
    }
};
```

#### tcp chat

> ES5

```javascript
var net = require('net');
require('colors');

var PORT = 4000;

function tcpChat() {
    var count = 0;
    var userCount = {};
    var server = net.createServer(function (socket) {
        var nickName = '';

        function bucket(msg, expectUser) {
            for (var key in userCount) {
                if (!expectUser || key !== nickName) {
                    userCount[key].write(msg);
                }
            }
        }

        socket.write('欢迎来到 node chat 聊天室!\n' +
            '共有 ' + count + ' 位用户在聊天室内~\n' +
            '请您输入自己的用户名: '.blue);
        count++;
        socket.on('data', function (buffer) {
            buffer = buffer.replace('\r\n', '');
            if (!nickName) {
                if (userCount[nickName]) {
                    socket.write('此用户名重复,请重新输入:');
                } else {
                    nickName = buffer;
                    userCount[nickName] = socket;
                    bucket(nickName + ' join this room~\n');
                }
            } else {
                bucket((nickName + ' > ' + buffer + '\n').cyan, true);
            }
        });
        socket.on('close', function () {
            count--;
            delete userCount[nickName];
            bucket(nickName + ' left this room\n');
        });
        socket.setEncoding('utf-8');
    });
    server.listen(PORT, function () {
        console.log('Server is running at ' + PORT + '~');
    });
}
```

> ES6

```javascript
const net = require('net');
require('colors');

const PORT = 4000;

const tcpChat = () => {
    let count = 0;
    const userAccount = {};
    const server = net.createServer(socket => {
        let nickName = '';
        const bucket = (msg, expectUser) => {
            for (const key in userAccount) {
                if (!expectUser || key !== nickName) {
                    userAccount[key].write(msg);
                }
            }
        };
        socket.write(`欢迎来到 node chat 聊天室!
共有 ${count} 位用户在聊天室内~
请您输入自己的用户名: `.blue);
        count++;
        socket.on('data', buffer => {
            buffer = buffer.replace('\r\n', '');
            if (!nickName) {
                if (userAccount[nickName]) {
                    socket.write('此用户名重复,请重新输入:');
                } else {
                    nickName = buffer;
                    userAccount[nickName] = socket;
                    bucket(`${nickName} join this room~\n`);
                }
            } else {
                bucket(`${nickName} > ${buffer}\n`.cyan, true);
            }
        });
        socket.on('close', () => {
            count--;
            delete userAccount[nickName];
            bucket(`${nickName} left this room~\n`);
        });
        socket.setEncoding('utf-8');
    });
    server.listen(PORT, () => {
        console.log(`Server is running at ${PORT}~`);
    });
};
```