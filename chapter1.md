#前言

## NodeJS

### 什么是Node

- Node.js 就是运行在服务端的 JavaScript。
- Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。
- Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。

### 什么是V8

- V8 JavaScript 引擎是 Google 用于其 Chrome 浏览器的底层 JavaScript 引擎。

### Node旨在解决什么问题

- Node 公开宣称的目标是 “旨在提供一种简单的构建可伸缩网络程序的方法”。
- 简单来说 ------ 更好的处理高并发。

### Node 解决这个问题的方法

- 更改连接到服务器的方式。每个连接发射一个在 Node 引擎的进程中运行的事件，而不是为每个连接生成一个新的 OS 线程（并为其分配一些配套内存）。
- Node 声称它绝不会死锁，因为它根本不允许使用锁,它不会直接阻塞 I/O 调用。
- Node 还宣称，运行它的服务器能支持数万个并发连接。

### Node如何工作？

- 运行在V8引擎基础上,运行在服务端
- 事件循环 --- 执行脚本时进入事件循环,没有带执行的回调任务时退出循环.
- HTTP --- 是一等公民,基于流,低延迟.适合作为网络框架的基础.
- 单线程 --- 但并不意味着无法利用多核CPU, child_process.fork() API 可以创建子进程,并能方便通信.在此基础上的cluster模块能够方便的实现负载均衡.

### Node安装

- 安装包
- 源码编译  --- 需要安装 Python 2.6 或 2.7


## NPM (Node Package Manager)

### 什么是NPM

- 本为Node包管理工具,但不限于node包
- 方便JSer共享和复用代码
- 方便进行代码依赖管理和升级

### 安装与升级NPM

- 安装node时已经默认安装了NPM
- 只需对其进行升级即可

```shell
    sudo npm install -g npm
```

### 安装package

- 本地安装
```shell
    npm install [package name]
```
- 全局安装
```shell
    sudo npm install -g [package name]
```
- 使用package.json

```shell
    // 当前初始化package.json
    npm init

    // 以下为对话式命令提示,以引导生成package.json
    name: (gulp)
    version: (1.0.0)
    description:
    git repository:
    keywords:
    license: (ISC)
    About to write to /Users/liuqi453/Tech/Gulp/package.json:

    {
     "name": "gulp",
     "version": "1.0.0",
     "main": "app.js",
     "directories": {
       "doc": "doc"
     },
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     "author": "Katsura",
     "license": "ISC",
     "description": ""
    }


    Is this ok? (yes)

```

```shell
    // 本地安装并添加到依赖
    npm install --save [package name]

    // 本地安装并添加到开发态依赖
    npm install --save-dev [package name]
```

### NPM的配置

- .npmrc文件 用户全局的.npmrc位于用户根目录下,项目的放在项目根目录下
- 命令

```shell
    // 设置代理
    npm config set proxy http://proxy.example.com:8080

    // 简写
    npm set proxy http://proxy.example.com:8080
```

### package.json

- name: package的名字（由于他会成为url的一部分，所以 non-url-safe 的字母不会通过，也不允许出现"."、"_"），最好先在 [npmjs源](http://registry.npmjs.org/)上搜下你取的名字是否已经存在
- version: package的版本，当package发生变化时，version也应该跟着一起变化，同时，你声明的版本需要通过semver的校验（semver可自行谷歌）
- dependencies: package的应用依赖模块，即别人要使用这个package，至少需要安装哪些东东。应用依赖模块会安装到当前模块的node_modules目录下。
- devDependencies：package的开发依赖模块，即别人要在这个package上进行开发




