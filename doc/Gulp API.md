# Gulp API

---

## 什么是 Gulp
 * 自动化构建工具
 * 强化工作流程

## 构建工作包括哪些
 - 编译文件
    - js文件编译，如CoffeeScript/TypeScript
    - css文件编译，如Less/Sass
 - 合理优化
    - 合并、压缩文件（html、js、css、img）
    - 静态资源js化
    - CDN化
    - 静态资源添加版本号
 - 配置
    - 分环境构建，替换特定标识为环境相关的数值，如dev/stg/prd
    - 指定特定标识，当做工程全局配置参数

## 还能做什么
 - 代码质量控制
    - 结合其他工具对工程进行自动化测试（单元测试、端到端测试）
    - 校验代码规范以及常见错误
 - 辅助开发
    - 设置文件监听，配合server实现代码变动时，浏览器自动刷新livereload
 - 打包发布
    - 分环境打包
    - 分环境发布

## API 
> 来自[中文官方文档](http://www.gulpjs.com.cn/docs/api/), 稍微进行了整理，以及补充说明。

### gulp.task
定义一个使用 Orchestrator 实现的任务（task）。

```javascript
gulp.task('somename', function() {
  // 做一些事
});
```

#### name

任务的名字，如果你需要在命令行中运行你的某些任务，那么，请不要在名字中使用空格。

#### deps
类型： Array
一个包含任务列表的数组，这些任务会在你当前任务运行之前完成。

``` JavaScript
gulp.task('mytask', ['array', 'of', 'task', 'names'], function() {
  // 做一些事
});
```

> 注意： 你的任务是否在这些前置依赖的任务完成之前运行了？请一定要确保你所依赖的任务列表中的任务都使用了正确的异步执行方式：使用一个 callback，或者返回一个 promise 或 stream。

#### fn
该函数定义任务所要执行的一些操作。通常来说，它会是这种形式：gulp.src().pipe(someplugin())。

##### 异步任务支持
任务可以异步执行，如果 fn 能做到以下其中一点：

 - 接受一个 callback

``` JavaScript
// 在 shell 中执行一个命令
var exec = require('child_process').exec;
gulp.task('jekyll', function(cb) {
  // 编译 Jekyll
  exec('jekyll build', function(err) {
    if (err) return cb(err); // 返回 error
    cb(); // 完成 task
  });
});
```
 - 返回一个 stream
``` JavaScript
gulp.task('somename', function() {
  var stream = gulp.src('client/**/*.js')
    .pipe(minify())
    .pipe(gulp.dest('build'));
  return stream;
});
```
 - 返回一个 promise
``` JavaScript
var Q = require('q');
gulp.task('somename', function() {
  var deferred = Q.defer();
  
  // 执行异步的操作
  setTimeout(function() {
    deferred.resolve();
  }, 1);

  return deferred.promise;
});
```
> 注意： 默认的，task 将以最大的并发数执行，也就是说，gulp 会一次性运行所有的 task 并且不做任何等待。如果你想要创建一个序列化的 task 队列，并以特定的顺序执行，你需要做两件事：

给出一个提示，来告知 task 什么时候执行完毕，
并且再给出一个提示，来告知一个 task 依赖另一个 task 的完成。
对于这个例子，让我们先假定你有两个 task，"one" 和 "two"，并且你希望它们按照这个顺序执行：

在 "one" 中，你加入一个提示，来告知什么时候它会完成：可以再完成时候返回一个 callback，或者返回一个 promise 或 stream，这样系统会去等待它完成。

在 "two" 中，你需要添加一个提示来告诉系统它需要依赖第一个 task 完成。

因此，这个例子的实际代码将会是这样：

``` JavaScript
var gulp = require('gulp');

// 返回一个 callback，因此系统可以知道它什么时候完成
gulp.task('one', function(cb) {
    // 做一些事 -- 异步的或者其他的
    cb(err); // 如果 err 不是 null 或 undefined，则会停止执行，且注意，这样代表执行失败了
});

// 定义一个所依赖的 task 必须在这个 task 执行之前完成
gulp.task('two', ['one'], function() {
    // 'one' 完成后
});

gulp.task('default', ['one', 'two']);
``` 

### gulp.src 与 gulp.dest

#### gulp.src(globs[, options])
输出（Emits）符合所提供的匹配模式（glob）或者匹配模式的数组（array of globs）的文件。 将返回一个 Vinyl files 的 stream 它可以被 piped 到别的插件中。

```javascript
gulp.src('client/templates/*.jade')
  .pipe(jade())
  .pipe(minify())
  .pipe(gulp.dest('build/minified_templates'));
```
> glob 请参考 [node-glob](https://github.com/isaacs/node-glob) 语法 或者，你也可以直接写文件的路径。

#### gulp.dest(path[, options])
能被 pipe 进来，并且将会写文件。并且重新输出（emits）所有数据，因此你可以将它 pipe 到多个文件夹。如果某文件夹不存在，将会自动创建它。

```javascript
gulp.src('./client/templates/*.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates'))
  .pipe(minify())
  .pipe(gulp.dest('./build/minified_templates'));
```
文件被写入的路径是以所给的相对路径根据所给的目标目录计算而来。类似的，相对路径也可以根据所给的 base 来计算。
### gulp.watch
监视文件，并且可以在文件发生改动时候做一些事情。它总会返回一个 EventEmitter 来发射（emit） change 事件。

#### gulp.watch(glob[, opts], tasks)

##### glob
类型： String or Array
一个 glob 字符串，或者一个包含多个 glob 字符串的数组，用来指定具体监控哪些文件的变动。

##### opts
类型： Object
传给 [gaze](https://github.com/shama/gaze) 的参数。

> **Gaze：** 一个基于其他几个很好的监听库中的精华部分而构建的模式匹配的 ***fs.watch*** 包装器。
**options** 需要传入的 ***options*** 对象.
1. **interval** *{integer}* 传给 fs.watchFile 方法的代表轮询时间间隔的数字
2. **debounceDelay** *{integer}* 对于相同文件或者事件执行成功回调延时的毫秒数
3. **mode** *{string}* 指定监听模式. 'auto' (默认)、 'watch' (基于原生事件)、 或者 'poll' (使用状态轮询).
4. **cwd** *{string}* 相对于 gen'mu'l的当前工作目录. 默认为 process.cwd().

##### tasks
类型： Array
需要在文件变动后执行的一个或者多个通过 gulp.task() 创建的 task 的名字，

```JavaScript
var watcher = gulp.watch('js/**/*.js', ['uglify','reload']);
watcher.on('change', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});
```
#### gulp.watch(glob[, opts, cb])

#####glob
类型： String or Array
一个 glob 字符串，或者一个包含多个 glob 字符串的数组，用来指定具体监控哪些文件的变动。

#####opts
类型： Object
传给 gaze 的参数。

#####cb(event)
类型： Function
每次变动需要执行的 callback。

``` JavaScript
gulp.watch('js/**/*.js', function(event) {
  console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
});
```

callback 会被传入一个名为 event 的对象。这个对象描述了所监控到的变动：

 - event.type
类型： String
发生的变动的类型：added, changed 或者 deleted。

 - event.path
类型： String
触发了该事件的文件的路径。
