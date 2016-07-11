# Gulp 插件 —— gulp-load-plugins

> - 自动加载 **package.json** 中的任何插件
- 可以从包配置文件中加载插件，并根据你的需要将他们挂在任何对象上

## 用法

首先，给定一个 **package.json** ，里面定义好了一些需要依赖的包。

```json
{
    "dependencies": {
        "gulp-jshint": "*",
        "gulp-concat": "*"
    }
}
```

然后，在你的 **gulpfile.js** 中添加如下几行代码

```javascript
var gulp = require('gulp');
var gulpLoadPlugins = require('gulp-load-plugins');
var plugins = gulpLoadPlugins();
```
或者，使用简写

```javascript
var plugins = require('gulp-load-plugins')();
```

接下来，会发生神奇的事情（大体上，插件都是懒加载的，但是实际上并不会感觉有任何不同）：

```javascript
plugins.jshint = require('gulp-jshint');
plugins.concat = require('gulp-concat');
```

这样，你可以像手动加载的情况下那样去使用它们，但是引用方式发生了变化，你应该使用 ***plugins.jshint()*** 而不再是 ***jshint()***.

可喜可贺！这样你的双手得以解放，你不必再手动去 **require** 这些插件。

## Options

你可以传入可选的配置项，就像下面这样，里面写的都是这些配置项的默认值：

```javascript
gulpLoadPlugins({
    DEBUG: false, // 为 ***true***, 插件会在控制台打印日志。在提交 bug 或者 issue debugging 时将很有用。
    pattern: ['gulp-*', 'gulp.*'], // 需要查找模式
    config: 'package.json', // 指定哪里去寻找插件，默认从 ***process.cwd()*** 开始查找。
    scope: ['dependencies', 'devDependencies', 'peerDependencies'], // 指定查找配置中那些 keys 下面的插件。
    replaceString: /^gulp(-|\.)/, // 指定将模块对象加载到上下文中时，需要移除的插件名字的哪个部分。
    camelize: true, // 如果设置为 ***true*** 时，将 *gulp-plugin-name* 重命名为 *gulpPluginName*。
    lazy: true, // 是否启用按需加载
    rename: {}, // 插件别名的对应关系
    renameFn: function (name) { ... } // 处理插件重命名的方法，未指定则使用默认规则
});
```

## 重命名

从 0.8.0开始，你可以向本插件指定一个各依赖包别名的对应关系。例如，你需要使用 *gulp-ruby-sass*, 但是你想要使用 *sass* 来使用这个插件

```javascript
gulpLoadPlugins({
  rename: {
    'gulp-ruby-sass': 'sass'
  }
});
```
> 注意，如果指定了你自己的 ***renameFn*** 方法，这时你配置的 ***rename*** 选项依然生效，但是 ***replaceString*** 和 ***camelize*** 将会被忽略。

## npm Scopes

***gulp-load-plugins*** 支持 npm scope，主要的区别在于，你需要通过 ***plugins*** 对象上的对应于 *scope* 的属性来访问。例如：*@myco/gulp-test-plugin* 这个插件，你需要使用下面的方式来访问：

```javascript
var plugins = require('gulp-load-plugins')();
plugins.myco.testPlugin();
```

## Lazy Loading

在 0.4.0以及之前版本，懒加载仅仅适用于返回一个方法的插件。但是在之后的版本中，它适用于任何插件。如果你遇到了相关的问题，试着关闭懒加载，看是否能解决问题。


## Credit

本插件大部分归功于 ***@sindresorhus*** 的 ***load-grunt-plugins*** 插件，几乎与它是相同的，只是稍微做了些修改来适配 gulp、来暴露出所依赖插件的引用。