---
title: think of tapable
date: 2022-10-15 02:00:37
tags: [webpack, tapable]
categories: webpack
---
# Tapable

> 什么是 Tapable

  简单来说,类似于 EventEmitter 的一种发布订阅模式,也就是观察者模式,但不同的是,Tapable 的流程以及应用场景要广泛复杂的多.Tapable 作为 Webpack 的主要骨架而流行,因此也受到前端开发爱好者的研究.

> 订阅模式类型 —— Hook

  在 Tapable 中订阅模式类型被称为 Hook,也就是 "钩子",基本分为四种.

  - 普通钩子
  - 熔断钩子
  - 瀑布钩子
  - 循环钩子

  在此分类基础上,再添加发布时同步(Sync)、异步(Async)以及 Promise 的区分.

> 整体源码分析

  先按照整体流程大体看一遍源码,再具体分析各个分类,就先拿最简单 SyncHook 同步普通钩子举例.

  ```javascript
    /*
        MIT License http://www.opensource.org/licenses/mit-license.php
        Author Tobias Koppers @sokra
    */
    "use strict";
    
    const Hook = require("./Hook");
    const HookCodeFactory = require("./HookCodeFactory");
    
    // 内容则是由 HookCodeFactory 生成
    class SyncHookCodeFactory extends HookCodeFactory {
        content({ onError, onDone, rethrowIfPossible }) {
            return this.callTapsSeries({
                onError: (i, err) => onError(err),
                onDone,
                rethrowIfPossible
            });
        }
    }
    
    const factory = new SyncHookCodeFactory();
    
    // 继承自 Hook 源类
    class SyncHook extends Hook {
        // 同步的订阅模式类型 Hook 不能调用 tapAsync、tapPromise 异步订阅方法
        tapAsync() {
            throw new Error("tapAsync is not supported on a SyncHook");
        }
    
        tapPromise() {
            throw new Error("tapPromise is not supported on a SyncHook");
        }
        
        // 编译方法生成并导出最终的函数
        compile(options) {
            factory.setup(this, options);
            return factory.create(options);
        }
    }
    
    module.exports = SyncHook;
  ```

  可以看出每一种订阅模式类型 Hook 是继承自 Hook 源类,而内容则是由 HookCodeFactory 生成的,特定的订阅模式类型也会做对应的限制,如同步订阅类型的 Hook 只能调用 tap 同步订阅方法,却不能调用其他异步订阅方法.继续看 Hook 源类,备注会详细说明每个主要的方法做的事情.

  ```javascript
    /*
        MIT License http://www.opensource.org/licenses/mit-license.php
        Author Tobias Koppers @sokra
    */
    "use strict";
    
    class Hook {
        // 构造对象拥有的属性
        constructor(args) {
            if (!Array.isArray(args)) args = [];
            // 构造时指定的参数名称和个数
            this._args = args;
            // 订阅的方法集合
            this.taps = [];
            // 拦截器集合
            this.interceptors = [];
            // 不同类型的发布方法拥有不同处理订阅模式的方式
            // call 对应 sync (同步)
            // promise 对应 promise 处理
            // callAsync 对应 async (异步)
            this.call = this._call;
            this.promise = this._promise;
            this.callAsync = this._callAsync;
            this._x = undefined;
        }

        compile(options) {
            throw new Error("Abstract: should be overriden");
        }
    
        // 将根据发布时的类型、构造时指定的参数名称、拦截器以及订阅的方法集合编译生成
        _createCall(type) {
            return this.compile({
                taps: this.taps,
                interceptors: this.interceptors,
                args: this._args,
                type: type
            });
        }
    
        // 同步订阅模式类型处理方法
        tap(options, fn) {
            if (typeof options === "string") options = { name: options };
            //...
            options = Object.assign({ type: "sync", fn: fn }, options);
            //...
            this._insert(options);
        }

        // 异步订阅模式类型处理方法
        tapAsync(options, fn) {
            if (typeof options === "string") options = { name: options };
            //...
            options = Object.assign({ type: "async", fn: fn }, options);
            //...
            this._insert(options);
        }

        // promise 订阅模式类型处理方法
        tapPromise(options, fn) {
            if (typeof options === "string") options = { name: options };
            //...
            options = Object.assign({ type: "promise", fn: fn }, options);
            //...
            this._insert(options);
        }
    
        //...

        // 不同类型的发布方法拥有不同处理订阅模式的方式
        // call 对应 sync (同步)
        // promise 对应 promise 处理
        // callAsync 对应 async (异步)
        _resetCompilation() {
            this.call = this._call;
            this.callAsync = this._callAsync;
            this.promise = this._promise;
        }
    
        // 最终会按照 {type: string, name: string, fn: Function} 的方式添加至订阅方法集合中.
        //           type: 必填,必须为 'sync'、'async' 和 'promise' 三种枚举选项之一.
        //           name: 必填,添加订阅方法时做的简要描述.
        //           fn: 必填,添加的订阅方法本身.
        _insert(item) {
            this._resetCompilation();
            let i = this.taps.length;
            //...
            this.taps[i] = item;
        }
    }
    
    // 返回一个函数,而这个函数也就是发布时调用的 call(args1), callAsync(args1, callback), promise(args1)
    function createCompileDelegate(name, type) {
        return function lazyCompileHook(...args) {
            this[name] = this._createCall(type);
            return this[name](...args);
        };
    }
    
    // Hook 源类原型链自带 _call、_promise、_callAsync 方法.
    Object.defineProperties(Hook.prototype, {
        _call: {
            value: createCompileDelegate("call", "sync"),
            configurable: true,
            writable: true
        },
        _promise: {
            value: createCompileDelegate("promise", "promise"),
            configurable: true,
            writable: true
        },
        _callAsync: {
            value: createCompileDelegate("callAsync", "async"),
            configurable: true,
            writable: true
        }
    });
    
    module.exports = Hook;
  ```

  可以看出 Hook 的源类经过一番整合处理,将根据发布时的类型、构造时指定的参数名称、拦截器以及订阅的方法集合通过编译生成,最终都指向了 compile 方法内的 HookCodeFactory 构造对象.那么最终需要克服的就是 HookCodeFactory 类,备注会详细说明每个主要的方法做的事情.

  ```javascript
    /*
        MIT License http://www.opensource.org/licenses/mit-license.php
        Author Tobias Koppers @sokra
    */
    "use strict";
    
    class HookCodeFactory {
        constructor(config) {
            this.config = config;
            this.options = undefined;
            this._args = undefined;
        }
    
        // 初始化后,根据不同的发布时的类型,返回填充不同的内容的匿名函数.
        create(options) {
            this.init(options);
            let fn;
            switch (this.options.type) {
                // 这里后续会更加详尽对比的做讲解
                // ...
            }
            // 将构造对象属性重置
            this.deinit();
            return fn;
        }
    
        // 将处理后订阅方法的集合指向构造对象本身,相当于 this._x = [fn1, fn2, fn3, fn4, ...]
        setup(instance, options) {
            instance._x = options.taps.map(t => t.fn);
        }
    
        /**
         * @param {{ type: "sync" | "promise" | "async", taps: Array<Tap>, interceptors: Array<Interceptor> }} options
         */
        // 实行初始化,将传入的参数以及参数中构造时指定的参数名称进行构造对象属性赋值
        // this.options = {taps: [{type: string, fn: Function, name: string}], type: string, interceptors: Array<Interceptor>, args: Array<String>}
        // this.args = Array<String>
        init(options) {
            this.options = options;
            this._args = options.args.slice();
        }

        // 将构造对象属性重置
        deinit() {
            this.options = undefined;
            this._args = undefined;
        }
    
        // 这里后续会更加详尽对比的做讲解
        // ...
    }
    
    module.exports = HookCodeFactory;
  ```
  
  由上面的流程,可以基本理清 Tapable Hook 发布订阅模式的关系图,其实还是比较简单的:

  ![](https://image.white-than-wood.zone/webpack/tapable.png)

  接下来将是重头戏,将根据不同的订阅模式类型、不同的发布类型以及构造时指定的参数名称编译生成拼接好不同的内容的匿名函数.
  
  - 首先是根据不同的发布类型以及构造时指定的参数名称实行第一步拼接.

      ```javascript
      switch (this.options.type) {
        case "sync":
            fn = new Function(
                this.args(),
                '"use strict";\n' +
                this.header() +
                this.content({
                    onError: err => `throw ${err};\n`,
                    onResult: result => `return ${result};\n`,
                    resultReturns: true,
                    onDone: () => "",
                    rethrowIfPossible: true
                })
            );
            break;
        case "async":
            fn = new Function(
                this.args({
                    after: "_callback"
                }),
                '"use strict";\n' +
                this.header() +
                this.content({
                    onError: err => `_callback(${err});\n`,
                    onResult: result => `_callback(null, ${result});\n`,
                    onDone: () => "_callback();\n"
                })
            );
            break;
        case "promise":
            let errorHelperUsed = false;
            const content = this.content({
                onError: err => {
                    errorHelperUsed = true;
                    return `_error(${err});\n`;
                },
                onResult: result => `_resolve(${result});\n`,
                onDone: () => "_resolve();\n"
            });
            let code = "";
            code += '"use strict";\n';
            code += "return new Promise((_resolve, _reject) => {\n";
            if (errorHelperUsed) {
                code += "var _sync = true;\n";
                code += "function _error(_err) {\n";
                code += "if(_sync)\n";
                code += "_resolve(Promise.resolve().then(() => { throw _err; }));\n";
                code += "else\n";
                code += "_reject(_err);\n";
                code += "};\n";
            }
            code += this.header();
            code += content;
            if (errorHelperUsed) {
                code += "_sync = false;\n";
            }
            code += "});\n";
            fn = new Function(this.args(), code);
            break;
      }
      ```

      ```javascript
      /*
            MIT License http://www.opensource.org/licenses/mit-license.php
            Author Tobias Koppers @sokra
      */
      "use strict";
    
      class HookCodeFactory {
          //...
          header() {
              let code = "";
              if (this.needContext()) {
                  code += "var _context = {};\n";
              } else {
                  code += "var _context;\n";
              }
              code += "var _x = this._x;\n";
              if (this.options.interceptors.length > 0) {
                  code += "var _taps = this.taps;\n";
                  code += "var _interceptors = this.interceptors;\n";
              }
              for (let i = 0; i < this.options.interceptors.length; i++) {
                  const interceptor = this.options.interceptors[i];
                  if (interceptor.call) {
                      code += `${this.getInterceptor(i)}.call(${this.args({
                          before: interceptor.context ? "_context" : undefined
                      })});\n`;
                  }
              }
              return code;
          }
          //...
      }
      ```
    
      ```javascript
      /*
            MIT License http://www.opensource.org/licenses/mit-license.php
            Author Tobias Koppers @sokra
        */
      "use strict";
    
      class HookCodeFactory {
          //...
          args({before, after} = {}) {
              let allArgs = this._args;
              if (before) allArgs = [before].concat(allArgs);
              if (after) allArgs = allArgs.concat(after);
              if (allArgs.length === 0) {
                  return "";
              } else {
                  return allArgs.join(", ");
              }
          }
          //...
      }
      ```
    
      添加上 header 以及 args 部分,乍看起来是有点多的,可以使用伪代码来简化流程:
    
      ```
      fn
      if 'sync'
        fn = args + header + content
      if 'async'
        fn = args with _callback + header + content
      if 'promise'
        fn = (args + header + content) with new Promise
      ```
    
      也可以转化为实际拼接好的 JS 代码来表示流程:
    
      ```javascript
      this.call = function lazyCompileHook(...args) {
        return (function (args1, args2) {
            'use strict';
            var _context;
            var _x = this._x;
            // content({onError, onResult, resultReturns, onDone, rethrowIfPossible});
        })(...args);
      }
      ```
    
      ```javascript
      this.callAsync = function lazyCompileHook(...args) {
        return (function (args1, args2, _callback) {
            'use strict';
            var _context;
            var _x = this._x;
            // content({onError, onResult, onDone});
        })(...args);
      }
      ```
    
      ```javascript
      this.promise = function lazyCompileHook(...args) {
        return (function (args1, args2, _callback) {
            'use strict';
            return new Promise((_resolve, _reject) => {
                var _context;
                var _x = this.x;
                // content({onError, onResult, onDone});
            });
        })(...args);
      }
      ```

  - 接着着重看下一个部分,也就是根据不同的订阅模式类型实行最后的拼接.

    ```javascript
          //先看 SyncxxxHook 部分,除了循环钩子,调用的都是 callTapsSeries 方法
          class HookCodeFactory {
              //...
              callTapsSeries({
                                 onError,
                                 onResult,
                                 resultReturns,
                                 onDone,
                                 doneReturns,
                                 rethrowIfPossible
                             }) {
                  if (this.options.taps.length === 0) return onDone();
                  const firstAsync = this.options.taps.findIndex(t => t.type !== "sync");
                  const somethingReturns = resultReturns || doneReturns || false;
                  let code = "";
                  let current = onDone;
                  for (let j = this.options.taps.length - 1; j >= 0; j--) {
                      const i = j;
                      //...
                      const done = current;
                      //...
                      const content = this.callTap(i, {
                          onError: error => onError(i, error, done, doneBreak),
                          onResult:
                              onResult &&
                              (result => {
                                  return onResult(i, result, done, doneBreak);
                              }),
                          onDone: !onResult && done,
                          rethrowIfPossible:
                              rethrowIfPossible && (firstAsync < 0 || i < firstAsync)
                      });
                      current = () => content;
                  }
                  code += current();
                  return code;
              }
    
              //...
          }
    ```
    
    由上述部分可以看出,逆循环订阅方法集合,本次拼接的结果会作为下一次拼接的 onDone 或者 onResult 传入至 callTap 方法中.那就继续查看 callTap 方法.

    ```javascript
        // 相对应的,还是查看 'sync' 部分
        class HookCodeFactory {
            //...
            callTap(tapIndex, {onError, onResult, onDone, rethrowIfPossible}) {
                let code = "";
                //...
                code += `var _fn${tapIndex} = ${this.getTapFn(tapIndex)};\n`;
                const tap = this.options.taps[tapIndex];
                switch (tap.type) {
                    case "sync":
                        if (!rethrowIfPossible) {
                            code += `var _hasError${tapIndex} = false;\n`;
                            code += "try {\n";
                        }
                        if (onResult) {
                            code += `var _result${tapIndex} = _fn${tapIndex}(${this.args({
                                before: tap.context ? "_context" : undefined
                            })});\n`;
                        } else {
                            code += `_fn${tapIndex}(${this.args({
                                before: tap.context ? "_context" : undefined
                            })});\n`;
                        }
                        if (!rethrowIfPossible) {
                            code += "} catch(_err) {\n";
                            code += `_hasError${tapIndex} = true;\n`;
                            code += onError("_err");
                            code += "}\n";
                            code += `if(!_hasError${tapIndex}) {\n`;
                        }
                        if (onResult) {
                            code += onResult(`_result${tapIndex}`);
                        }
                        if (onDone) {
                            code += onDone();
                        }
                        if (!rethrowIfPossible) {
                            code += "}\n";
                        }
                        break;
                    //...
                }
                return code;
            }
            //...
        }
    ```
    
    ```javascript
    class HookCodeFactory {
        //...
        getTapFn(idx) {
            return `_x[${idx}]`;
        }
        //...
    }
    ```
  
    至此,基本可以得到结论,在 callTap 方法中,'sync' 部分是根据有无 onResult 以及有无 onDone 来判断并拼接内容的.那最后再简化一下.

    <div style="display: flex;justify-content: space-between;align-items: center;">
        <div style="width: 49%;">
            <img src="https://image.white-than-wood.zone/webpack/on_result.png" alt="on_result">
        </div>
        <div style="width: 48%;">
            <img src="https://image.white-than-wood.zone/webpack/on_done.png" alt="on_done">
        </div>
    </div>

    对于 SyncxxxHook 部分,除了循环钩子,最终得出了答案.

    ```javascript
        // SyncHook,在这里都使用多参数名称、多订阅方法集合来展示最终拼接结果.
        this.call = function lazyCompileHook(...args) {
            return (function (args1, args2) {
                'use strict';
                var _context;
                var _x = this._x;
                var _fn0 = this._x[0];
                _fn0(args1, args2);
                var _fn1 = this._x[1];
                _fn1(args1, args2);
            })(...args);
        }
    ```

    ```javascript
        // SyncBailHook,在这里都使用多参数名称、多订阅方法集合来展示最终拼接结果.
        this.call = function lazyCompileHook(...args) {
            return (function (args1, args2) {
                'use strict';
                var _context;
                var _x = this._x;
                var _fn0 = this._x[0];
                var _result0 = _fn0(args1, args2);
                if (_result0 !== undefined) {
                    return _result0;
                } else {
                    var _fn1 = this._x[1];
                    var _result1 = _fn1(args1, args2);
                    if (_result1 !== undefined) {
                        return _result1;
                    } else {}
                }
            })(...args);
        }
    ```

    ```javascript
    // SyncWarterfallHook,在这里都使用多参数名称、多订阅方法集合来展示最终拼接结果.
    this.call = function lazyCompileHook(...args) {
        return (function (args1, args2) {
            'use strict';
            var _context;
            var _x = this._x;
            var _fn0 = _x[0];
            var _result0 = _fn0(args1, args2);
            if (_result0 !== undefined) {
                args1 = _result0;
            }
            var _fn1 = _x[1];
            var _result1 = _fn1(args1, args2);
            if (_result1 !== undefined) {
                args1 = _result1;
            }
            return args1;
        })(...args);
    }
    ```
    
    以此类推,异步 AsyncSeriesxxxHook 部分,除了循环钩子,调用的也都是 callTapsSeries 方法.

    ```javascript
          class HookCodeFactory {
              //...
              callTapsSeries({
                                 onError,
                                 onResult,
                                 resultReturns,
                                 onDone,
                                 doneReturns,
                                 rethrowIfPossible
                             }) {
                  if (this.options.taps.length === 0) return onDone();
                  const firstAsync = this.options.taps.findIndex(t => t.type !== "sync");
                  const somethingReturns = resultReturns || doneReturns || false;
                  let code = "";
                  let current = onDone;
                  for (let j = this.options.taps.length - 1; j >= 0; j--) {
                      const i = j;
                      if (unroll) {
                          code += `function _next${i}() {\n`;
                          code += current();
                          code += `}\n`;
                          current = () => `${somethingReturns ? "return " : ""}_next${i}();\n`;
                      }
                      const done = current;
                      const doneBreak = skipDone => {
                          if (skipDone) return "";
                          return onDone();
                      };
                      const content = this.callTap(i, {
                          onError: error => onError(i, error, done, doneBreak),
                          onResult:
                              onResult &&
                              (result => {
                                  return onResult(i, result, done, doneBreak);
                              }),
                          onDone: !onResult && done,
                          rethrowIfPossible:
                              rethrowIfPossible && (firstAsync < 0 || i < firstAsync)
                      });
                      current = () => content;
                  }
                  code += current();
                  return code;
              }
              //...
          }
    ```

    同理,异步 AsyncSeriesxxxHook 部分还是逆循环订阅方法集合,本次拼接的结果会作为下一次拼接的 onDone 或者 onResult 传入至 callTap 方法中,但是本次拼接的结果会加一层 next 包裹,接着还是查看 callTap 方法.

    ```javascript
            // 相对应的,查看 'async' 部分
            class HookCodeFactory {
                //...
                callTap(tapIndex, {onError, onResult, onDone, rethrowIfPossible}) {
                    let code = "";
                    //...
                    code += `var _fn${tapIndex} = ${this.getTapFn(tapIndex)};\n`;
                    const tap = this.options.taps[tapIndex];
                    switch (tap.type) {
                        case "async":
                            let cbCode = "";
                            if (onResult) cbCode += `(_err${tapIndex}, _result${tapIndex}) => {\n`;
                            else cbCode += `_err${tapIndex} => {\n`;
                            cbCode += `if(_err${tapIndex}) {\n`;
                            cbCode += onError(`_err${tapIndex}`);
                            cbCode += "} else {\n";
                            if (onResult) {
                                cbCode += onResult(`_result${tapIndex}`);
                            }
                            if (onDone) {
                                cbCode += onDone();
                            }
                            cbCode += "}\n";
                            cbCode += "}";
                            code += `_fn${tapIndex}(${this.args({
                                before: tap.context ? "_context" : undefined,
                                after: cbCode
                            })});\n`;
                            break;
                        //...
                    }
                    return code;
                }
    
                //...
            }
    ```

    ```javascript
    class HookCodeFactory {
        //...
        getTapFn(idx) {
            return `_x[${idx}]`;
        }
        //...
    }
    ```

    至此,也基本可以得到结论,在 callTap 方法中,'async' 部分是根据 onError、有无 onResult 以及有无 onDone 来判断并拼接内容的.最后也再简化一下.

    <div style="display: flex;justify-content: space-between;align-items: center;">
        <div style="width: 49%;">
            <img src="https://image.white-than-wood.zone/webpack/async_on_result.png" alt="async_on_result">
        </div>
        <div style="width: 49%;">
            <img src="https://image.white-than-wood.zone/webpack/async_on_done.png" alt="async_on_done">
        </div>
    </div>

    对于 AsyncSeriesxxxHook 部分,除了循环钩子,最终也得出了答案.

    ```javascript
        // AsyncSeriesHook,在这里都使用多参数名称、多异步订阅方法集合来展示最终拼接结果.
        this.call = function lazyCompileHook(...args) {
            return (function (args1, args2, _callback) {
                'use strict';
                var _context;
                var _x = this._x;
    
                function _next0() {
                    var _fn1 = _x[1];
                    _fn1(args1, args2, (_err1) => {
                        if (_err1) {
                            _callback(_err1);
                        } else {
                            _callback();
                        }
                    });
                }
    
                var _fn0 = _x[0];
                _fn0(args1, args2, (_err0) => {
                    if (_err0) {
                        _callback(_err0);
                    } else {
                        _next0();
                    }
                });
            })(...args);
        }
    ```

    ```javascript
        // AsyncSeriesBailHook,在这里都使用多参数名称、多异步订阅方法集合来展示最终拼接结果.
        this.call = function lazyCompileHook(...args) {
            return (function (args1, args2, _callback) {
                'use strict';
                var _context;
                var _x = this._x;
    
                function _next0() {
                    var _fn1 = _x[1];
                    _fn1(args1, args2, (_err1, _result1) => {
                        if (_err1) {
                            _callback(_err1);
                        } else {
                            if (_result1 !== undefined) {
                                _callback(null, _result1);
                            } else {
                                _callback();
                            }
                        }
                    });
                }
    
                var _fn0 = _x[0];
                _fn0(args1, args2, (_err0, _result0) => {
                    if (_err0) {
                        _callback(_err0);
                    } else {
                        if (_result0 !== undefined) {
                            _callback(null, _result0);
                        } else {
                            _next0();
                        }
                    }
                });
            })(...args);
        }
    ```

    ```javascript
        // AsyncSeriesWaterfallHook,在这里都使用多参数名称、多异步订阅方法集合来展示最终拼接结果.
        this.call = function lazyCompileHook(...args) {
            return (function (args1, args2, _callback) {
                'use strict';
                var _context;
                var _x = this._x;
    
                function _next0() {
                    var _fn1 = _x[1];
                    _fn1(args1, args2, (_err1, _result1) => {
                        if (_err1) {
                            _callback(_err1);
                        } else {
                            if (_result1 !== undefined) {
                                args1 = _result1;
                            }
                            _callback(null, args1);
                        }
                    });
                }
    
                var _fn0 = _x[0];
                _fn0(args1, args2, (_err0, _result0) => {
                    if (_err0) {
                        _callback(_err0);
                    } else {
                        if (_result0 !== undefined) {
                            args1 = _result0;
                        }
                        _next0();
                    }
                });
            })(...args);
        }
    ```