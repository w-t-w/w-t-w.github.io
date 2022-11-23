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
const factorial = (n) => (n === 1) ? n : n * factorial(n - 1);
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