# Gulp 插件 —— gulp-plumber

> 防止 pipe 在插件发生错误时挂掉。

Briefly it replaces pipe method and removes standard onerror handler on error event, which unpipes streams on error by default.

它替换了 pipe 方法，移除了 *error* 事件的标准的 *onerror* 句柄，因为 *onerror* 默认会终止流。

## 用法

首先，作为开发依赖库安装 gulp-plumber ：

```shell
npm install --save-dev gulp-plumber
```

然后使用它：

```javascript
var plumber = require('gulp-plumber');
var coffee = require('gulp-coffee');
 
gulp.src('./src/*.ext')
	.pipe(plumber())
	.pipe(coffee())
	.pipe(gulp.dest('./dist'));
```

## API

### plumber([options])
返回一个Stream，它修复了接下来pipe流中的 **pipe** 方法。

 - options
 Type: Object / Function Default: {}
配置plumber的参数，如果是一个Function，那么它会用作 *errorhandler*

 - options.inherit
Type: Boolean Default: true
运行时替换(Monkeypatch)接下来管道(pipeline)中 *Stream* 的 **pipe** 方法.

 - options.errorHandler
Type: Boolean / Function Default: true
处理后续流中的错误，并输出到控制台。
可以传递不同值给此参数：
    - function - 它会被加到流的 on('error').
    - false - 不会使用任何 error 处理方法
    - undefined - 使用默认的错误处理

### plumber.stop()

这个方法会在完成 *pipe* 之后还原默认的 **pipe**。

```javascript
var plumber = require('gulp-plumber');
 
gulp.src('./src/*.scss')
    .pipe(plumber())
    .pipe(sass())
    .pipe(uglify())
    .pipe(plumber.stop())
    .pipe(gulp.dest('./dist'));
```