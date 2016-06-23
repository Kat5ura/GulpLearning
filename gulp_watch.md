# Gulp 插件 —— gulp-watch

> **[gulp-watch](https://www.npmjs.com/package/gulp-watch)** 是一个使用了超级快的[chokidar](https://github.com/paulmillr/chokidar)（一个node的fs.watch\fs.watchFile\fsevents的简洁封装）同时能够发送一个 [vinyl](https://github.com/gulpjs/vinyl) 对象的文件监听模块。

## API

### watch(glob, [options, callback])
 - 创建一个监听器来监听模式匹配到的文件，***glob***可以是一个字符串也可以是一个字符串数组。
 - 返回一个经过这个监听器的流，这个流会发送一个 [vinyl](https://github.com/gulpjs/vinyl) 文件对象，而这个文件又会触发文件系统的一个事件。

####Callback function(vinyl)
当文件系统的事件被触发时，这个回调方法将被调用。所有被pipe进来的文件都会被分组，然后被传递给事件流。

 - **vinyl** — 是一个 ***vinyl*** 对象，它对应着一个能够触发事件的文件。可以添加额外的 ***event*** 字段来指定具体的事件类型.
可能的事件：
    - **add** - 创建新文件或者新添加到监听器
    - **change** - 文件改变
    - **unlink** - 文件被删除
 
#### options

这个对象将直接传给 chokidar options， gulp.src的options也可以使用。如果你不希望watch来读取文件的内容，可以在options中添加 ***read: false***

 - options.ignoreInitial
类型： Boolean
默认值： true
> 决定是否监听最初的文件添加事件
 - options.events
类型： Array
默认值： ['add', 'change', 'unlink']
> 配置需要gulp-watch插件监听的事件。

 - options.base
类型： String
默认值： undefined
> 为模式匹配配置根路径。

 - options.name
类型： String
默认值： undefined
> 监听器的名字，如果指定了，你可以得到更多的可读输入
 - options.verbose
类型： Boolean
默认值： false
> 这个选项可以启用详细输入

 - options.readDelay
类型： Number
默认值： 10
> 读文件之前等待 readDelay 的时长

 - options.read
类型： Boolean
默认值： true
> 将此属性设置为 false，就不会读取文件，并且返回的file对象中的contents属性为 null。当你的管道流程并不关心文件内容时（如 clean任务），这将是一个很有用的可选项，它也可以你在回调中使用gulp.src 来读取文件而没有使用传入的file参数时，避免重复读取文件。

#### Method
返回的Stream对象有几个好用的实例方法：

 - add(path / paths)
 - unwatch(path / paths)
 - close()

#### Events
***chokidar*** 中定义的所有事件。

 - add, 
 - change, 
 - unlink, 
 - addDir, 
 - unlinkDir, 
 - error, 
 - ready, 
 - raw

### 使用示例

```javascript
var gulp = require('gulp'),
    watch = require('gulp-watch');
 
gulp.task('stream', function () {
	// Endless stream mode 
    return watch('css/**/*.css', { ignoreInitial: false })
        .pipe(gulp.dest('build'));
});
 
gulp.task('callback', function () {
	// Callback mode, useful if any plugin in the pipeline depends on the `end`/`flush` event 
    return watch('css/**/*.css', function () {
        gulp.src('css/**/*.css')
            .pipe(gulp.dest('build'));
    });
});
```

> 特别提醒：在 gulpjs 4.0 发布之前, 你可以使用 ***gulp-plumber*** 来防止在错误时中止进程。

