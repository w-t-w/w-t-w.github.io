---
title: think of nodejs
date: 2022-08-30 18:43:30
tags: [npm, nodejs]
categories: nodejs
---

# nodejs

#### 本 nodejs

> 概念

Nodejs 是一个基于 Chrome V8 引擎的 Javascript 运行环境

> 特性

- 事件驱动
- 非阻塞 I/O

> 非阻塞 I/O

实际上就是系统在接收输入与输出之间时,还可以继续处理其他输入

> 规范

nodejs 中的内置模块基本都遵从回调函数 callback(err, result) 的形式

> 区别

Javascript 分别在浏览器端和 Nodejs 服务端运行的最大区别: 

- Nodejs 服务端运行的 Javascript 不存在浏览器端的 document 和 window 等 API
- Nodejs 服务端运行的 Javascript 还新添加了很多独有的 API

> V8 引擎

在 Nodejs 中使用 V8 引擎是考虑到 V8 引擎性能的优秀性, 其只作用于浏览器端太可惜, 想让其能控制整个计算机

> 应用

- Web 网页端: 腾讯视频
- Electron 桌面应用端: VSCode
- Game 可自由的自定义各种模块的游戏端: 我的世界
- Bundler 构建打包工具: gulp、webpack

#### browserslist

> 作用

用于编译转化样式文件以兼容浏览器的对于各类浏览器版本的筛选.

> 分类

\[">0.2%"\]: 指的是现阶段的用户比例>0.2%的所有浏览器版本.
\["last 2 versions"\]: 指的是现阶段所有浏览器最近发布的两个版本.
\["not dead"\]: 指的是现阶段没有被删除销毁的所有浏览器版本.
\["defaults"\]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
\[">0.2%","last 2 versions","not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
\[">0.2% and last 2 versions and not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的交集.
\[">0.2% or last 2 versions or not dead"]: 相当于 ">0.2%"、"last 2 versions"以及"not dead" 浏览器版本的并集.
\["not >0.2%"\]: 指的是现阶段的用户比例 <=0.2% 的所有浏览器版本.

#### version

> 作用

用于发布构建/依赖/工具时,对于版本号的分类管理.

> 分类

- patch: 是对此构建/依赖/工具的补丁,常指修复一些Bug、代码样式美化以及变更文档等.
- minor: 是对此构建/依赖/工具在当前 API 下新增一个功能.
- major: 是对此构建/依赖/工具与当前 API 出现比较大的不兼容,也就是 BREAKING CHANGES 的重构.

#### pre-version

> 作用

先行版本号是作为构建/依赖/工具稳定版本生命周期的线性图,可确保其上线的质量、安全以及稳定性.

> 分类

- alpha: 是内测版本,一般会有很多 Bug,内测人员使用.
- beta: 测试版本,会开放一部分外部人员测试,这阶段还是会继续开发新功能.
- rc: 公测版本,会完全对外开放测试,不再开发添加新功能.

#### npx

> 作用

是 npm 自带的包执行器.其主要作用就是可随时执行项目内安装的可执行模块,且可在全局环境变量下脚本命令不存在时,自动安装执行.

> 原理

其原理非常简单,就是轮询 node_modules/.bin 子目录以及 $PATH 全局环境变量下是否存在指定的脚本命令,如果存在就执行,本地不存在则自动安装下载执行.

> 参数

- --no-install: 指的是强制使用本地模块,禁止下载远程模块.

#### inquirer

> 作用

是用于与用户进行简单互动的依赖,存在输入选择、判断选择、列表选择等互动方式.

```javascript
const inquirer = require('inquirer');
const prompt = inquirer.createPromptModule();
const env = prompt([{
    type: 'list',
    message: '请选择 webpack 构建打包时所属的环境:',
    name: 'env',
    choices: [
        'development',
        'production'
    ],
    filter(value) {
        return value.toLowerCase();
    }
}]);
const _env = env['env'];
console.log(_env);
```

#### shelljs

> 作用

是通过 js 新建 shell 脚本执行 shell 命令的依赖,简洁易用.

```javascript
const {exec} = require('shelljs');
exec('webpack');
```

#### yargs

> 作用

是用于解析命令行的依赖,可对命令行参数逐个解析,功能极其强大.

```javascript
const yargs = require('yargs');
const argv = process.argv;
const env = '';
yargs.parse(argv.slice(2), (err, argv) => {
    if (err) {
        console.error(err);
        throw new Error(err);
    }
    env = argv['env'];
});
console.log(env);
```

#### rimraf

> 作用

是用于递归剃掉删除项目目录的依赖,其绝对根目录是 node 所作用的位置目录,也就是 process.cwd().

```javascript
const rimraf = require('rimraf');
// 首参数: 相对于 node 所作用的位置目录.
// 回调函数: 在操作过后执行的步骤.
rimraf('./dist', () => {
    //...
    //...
});
```

#### spritesmith

> 作用

是用于合并多个图片形成雪碧图的依赖.

```javascript
const {run} = require('spritesmith');
// 位置目录必须为绝对路径
run({src: ['pic1.png', 'pic2.png']}, (err, image) => {
    // 会生成 16进制 Buffer
    console.log(image.image);
})
```

#### jszip

> 作用

用于使用 js 实现 zip 压缩打包的依赖.

```javascript
const JsZip = require('jszip');
const jszip = new JsZip();
// 生成空的压缩打包文件
jszip.folder('offline');
// 生成填入内容的压缩打包文件
jszip.file('index.js', 'console.log("wtw")');
// 将压缩打包文件实行各种数据类型转换
jszip.generatorAsync({type: 'nodebuffer'}).then((content) => {
    console.log(content);
});
  ```

> 自定义 webpack 插件以生成离线压缩包

```javascript
// webpack.config.js
const {resolve} = require('path');

// 自定义的生成离线压缩包的 webpack 插件
const JsZipPlugin = require('./lib/jsZipPlugin.js');

const OUTPUT_DIR = resolve(process.cwd(), './build');

const webpackConfig = {
    mode: 'production',
    entry: './src/index.js',
    output: {
        path: OUTPUT_DIR,
        filename: 'index.js'
    },
    plugins: [
        new JsZipPlugin({
            name: 'offline'
        })
    ]
};

module.exports = wbpackConfig;
```

```javascript
const JsZip = require('jszip');
const {RawSource} = require('webpack-sources');

class JsZipPlugin {
    constructor(options) {
        this.options = options;
    }

    // 所有的 webpack 插件都遵循其 tapable 插件体制,类似于 EnventEmitter 那种观察订阅模式
    apply(compiler) {
        const {name = ''} = this.options;
        const jszip = new JsZipPlugin();
        // 订阅生成文件时的 hooks 事件,emit 是 AsyncSeriesHook(异步串行 hook)
        compiler.hooks.emit.tapAsync('JsZipPlugin', (compilation, callback) => {
            // 生成空的压缩打包文件
            const folder = jszip.folder(name);
            for (let filename in compilation.assets) {
                // 生成填入 webpack 构建打包源代码的压缩打包文件
                folder.file(filename, compilation.assets[filename].source());
            }
            // 实行 nodebuffer 数据类型转换,将源代码转换为 buffer,并放入至 webpack 构建打包生成的资源模块内.
            jszip.generatorAsync({type: 'nodebuffer'}).then((content) => {
                compilation.assets[`${name}.zip`] = new RawSource(content);
                callback();
            });
        });
    }
}
```

#### import-local

> 作用

用于优先使用本地 node_modules 下的可执行模块,比如全局环境变量下的 webpack 脚本命令版本是 5.x,本地下载的 node_modules 下的 webpack 可执行模块脚本命令版本是 4.x,会优先使用本地下载的 node_modules 下的可执行模块脚本命令版本

#### express

> 特性

- 拥有强大的路由分发功能
- 对于 http 请求处理方面做了一系列的封装,比如说 302 重定向
- 可配合多种模板引擎
- 洋葱模型的机制,处理更加灵活

> 缺陷

- 洋葱模型机制不可处理异步事件

> 应用

- 比较适合于较小型的工程和应用

#### koa

> 特性

- 采用 async/await,解决了处理异步事件的问题
- 使用中间件、微内核的设计模式,更加关注性能

> 缺陷

- 由于采用微内核模式,不再拥有路由分发功能,如需要路由,则开发者自己设计中间件

> 应用

- 比较适合于大型的工程项目

#### RPC

> 特点

RPC 与 Ajax 对比可知:

- 寻址服务不一定基于 DNS,比如腾讯内网使用的 L5,阿里内网使用的 VIP(virtual IP)
- 基于 TCP 或者 UDP 协议,二进制数据传输,更小的数据包体积,更快的编解码速度,性能更好
- 通信方式
  - 单工通信: S/C 两端只能一端向另外一端推送数据包
  - 半双工通信: S/C 两端在一段时间内,只能一端向另外一端推送数据包(在一端接收到数据包之后,才可以继续此类操作)
  - 全双工通信: S/C 两端可自由推送数据包

#### GraphQL

> 概念

GraphQL 是 API 接口规范化查询方式(PS: 也可以为'查询语言')

#### 性能优化

> HTTP 性能测试

使用 apache bench 性能测试工具测试的结果要点:

- QPS: 每秒服务器所能承接的并发量
- Time Per Request: 每次请求的平均时间
- Transfer Rate: 吞吐量

> Javascript 代码优化

尽量的能够将所有的计算前置,使用空间去更换时间,来提高运行和计算效率

> 内存分配策略

如何更好的分配内存在于是否会使用池(pool)

