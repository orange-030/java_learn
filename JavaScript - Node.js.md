# Node.js

## 概念

Node JS is a Javascript runtime built on Chromes V8 javascript engine

Chromes V8 引擎是公认的解析 JavaScript 最快的引擎

Node.js 既不是语言，也不是框架，它是一个平台，是 JavaScript 的运行环境 

以前只有浏览器可以解析 JavaScript 代码，Node.js 可以使 JavaScript 脱离浏览器运行

Node.js 没有 DOM 和 BOM

Node.js 为 JavaScript 提供了一些服务器级别的 API

- 文件操作的能力
- HTTP 服务的能力



## 运行

```shell
node hello.js
```



# npm

## 概念

npm 是世界上最大的开源库生态系统





## package.json

建议每一个项目都要有一个 package.json，作为包描述文件。

```shell
npm init

// save参数可以在package.json保存依赖项
npm install jquery --save
```



## package-lock.json

npm 5 之后的版本不需要加`--save`参数，他会自动保存依赖信息

`package-lock.json` 这个文件会保存 `node_modules` 中所有包的信息（版本、下载地址）

---

lock的含义是锁，如果项目依赖了1.1.1版本，重新 `npm install` 会自动下载最新版本

`package-lock.json` 可以锁定版本号，防止自动升级新版本



# 模块系统

## 概念

在 Node 中，模块有三种：

- 具名的核心模块
- 用户自己编写的文件模块



Node 为 Javascript 提供了很多服务器级别的API，这些API绝大多数都被包装到了一个具名的核心模块中了例如

- 文件操作的 `fs` 模块，
- http服务构建的 `http` 模块 
- 路径操作 `path` 模块
- 操作系统 `os` 模块



## 加载模块

### require

require 方法有两个作用：

- 加载文件模块并执行里面的代码

- 获取被加载模块导出的接口对象



1. 加载具名的核心模块

   ```js
   fs = require('fs');
   ```

   

2. 加载自定义模块

   路径中的 `./` 不可省略，如果省略会被当做核心模块加载导致报错
   
   ```javascript
   utils = require('./utils')
   ```



### exports

```

```

