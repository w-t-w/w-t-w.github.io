---
title: create Simplepack
tags: ['webpack']
date: 2022-10-14 20:04:45
categories: webpack
---

# create Simplepack

搭建一个简易的 webpack —— Simplepack.

> 配置文件

```javascript
// simplepack.config.js
const {resolve} = require('path');

const OUTPUT_DIR = resolve(process.cwd(), './build');
const simplepackConfig = {
    // 入口
    entry: './src/index.js',
    // 作为 simplepack 构建打包的出口
    output: {
        // 作为 simplepack 构建打包文件导出的位置目录
        path: OUTPUT_DIR,
        // 作为 simplepack 构建打包导出文件的名称
        filename: 'main.js'
    }
};
module.exports = simplepackConfig;
```

> 目录

![](https://image.white-than-wood.zone/webpack/simplepack_path.png)

> Simplepack

```javascript
const {resolve} = require('path');
const {writeFileSync} = require('fs'); 
    
const utils = require('../utils');

class Simplepack {
    constructor(options) {
        this.options = options;
        this.resources = [];
    }

    // 运行
    run() {
        const {entry = ''} = this.options;
        if (entry) {
            throw new Error('找不到 webpack 构建打包的入口!');
        }
        // 递归获取资源列表
        (function getResources(path, sourcePath) {
            const compile = this.compile(path);
            compile.filename = sourcePath ? sourcePath : path;
            this.resources.push(compile);
            const {dependencies = []} = compile;
            if (dependencies.length > 0) {
                // 路径中是否存在类型文件
                const hasFile = /\.js$/;
                dependencies.forEach(item => {
                    const sourcePath = item;
                    // 拼接父子依赖目录
                    if (hasFile.test(path)) {
                        path = path.slice(0, path.lastIndexOf('/'));
                    }
                    if (!hasFile.test(item)) {
                        item += '/index.js';
                    }
                    path = resolve(path, item);
                    // 将拼接好的子依赖目录再次解析
                    getResources.call(this, path, sourcePath);
                });
            }
        }.bind(this))(entry);
        this.emitFile();
    }

    // 解析
    compile(path) {
        // 对相对路径进行校验,将相对路径转化为绝对路径
        const isRelative = /\.\/|\.\\/;
        if (isRelative.test(path)) {
            path = resolve(process.cwd(), path);
        }
        // 获取入口或者依赖模块的 AST 抽象语法树
        const ast = utils.getAST(path);
        return {
            // 获取相关依赖列表
            dependencies: utils.getDependencies(ast),
            // 获取相关代码
            source: utils.getTransformFromAST(ast)
        }
    }

    // 生成文件
    emitFile() {
        const {entry = '', output: {filename = '', path = ''}} = this.options;
        const outputPath = resolve(path, filename);
        // 模拟 webpack IIFE 自执行函数表达式闭包,将每一个模块外加一层包裹,并将 import 转化为 __WEBPACK_REQUIRE__
        const module = this.resources.map(item => `'${item.filename}': function(require, modules, exports) {${item.source}`).join(',');
        const bundle = `(function (module) {
            function require(filename) {
                var fn = module[filename];
                var modules = {exports: {}};
                fn(require, modules, modules.exports);
                return modules.exports;
            }
            require('${entry}');
        })({${module}})`;
        writeFileSync(outputPath, bundle);
    }
}
```

> Simplepack Parse 解析工具

```javascript
const {readFileSync} = require('fs');
const {parse} = require('babylon');
const {traverse, transformFromAST} = require('@babel/core');

const utils = {
    // 通过 babylon 解析入口或者依赖模块的抽象语法树
    getAST(path) {
        const content = readFileSync(path, 'utf-8');
        return parse(content, {
            sourceType: 'module'
        });
    },
    // 通过 traverse 遍历获取 import 导入的依赖模块
    getDependencies(ast) {
        const dependencies = [];
        traverse(ast, {
            ImportDeclaration({node}) {
                dependencies.push(node.source.value);
            }
        });
        return dependencies;
    },
    // 通过 transformFromAST 获取被 babel 处理后的代码
    getTransformFromAST(ast) {
        const {code} = transformFromAST(ast, null, {
            presets: ['@babel/preset-env']
        });
        return code;
    }
};

module.exports = utils;
```

> Result

```javascript
(function(module) {
    function require(filename) {
        var fn = module[filename];
        var modules = {exports: {}};
        fn(require, modules, exports);
        return modules.exports;
    }
    require('./src/index.js');
})({
    './src/index.js': function (require, modules, exports) {"use strict";

        var _modules = require("./modules");
        var wtw = (0, _modules.getName)('wtw');
        console.log('wtw');
        console.log(wtw);
    },
    './modules': function (require, modules, exports) {"use strict";

        Object.defineProperty(exports, "__esModule", {
            value: true
        });
        exports.getName = getName;
        function getName(name) {
            return "My name is ".concat(name);
        }
    }
});
```

