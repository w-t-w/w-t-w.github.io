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

#### layer flat

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

#### tailCall fibonacci

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

#### cache fibonacci

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

#### tailCall factorial

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

#### shallow copy

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

#### deep clone

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

#### deep clone loop

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

  - 首先 JS 线程会区分异/同步程序,遇到同步程序优先执行,遇到异步程序会将其移入至相关的处理线程(定时器线程、异步 HTTP 请求线程)
  - 接着,会将相关的处理线程符合条件的回调事件移入至事件触发线程内,进入 Event Loop 事件循环执行机制
  - Event Loop 会等待至同步程序全部执行完后,轮询事件队列中的回调事件,触发执行
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
      - MutaionObserver

> nodejs

  nodejs 中的 Event Loop 就有所不同, 其运行的阶段分为:

  - timer: 执行 setTimeout、setInterval 回调事件
  - pending callbacks: 执行延迟到下一个 I/O 回调
  - idle,prepare: 仅系统内部使用
  - poll: 检索新的 I/O 事件,执行与 I/O 相关的回调
  - check: 执行 setImmediate 回调 
  - close callbacks: 执行一些关闭的回调函数,如 socket.close 

  注意: poll 队列执行完后,如果没有 setImmediate,但是有定时器到期,会绕回执行定时器 timer 阶段

#### Promise

> ES5

```javascript

```

> ES6

```javascript

```