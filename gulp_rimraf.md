# Gulp 插件 —— gulp-rimraf

> Gulp 的 **[rimraf](https://github.com/isaacs/rimraf)** 插件。

## rimraf

这里先介绍下 rimraf 。

> 它是node版本的 *nix 系统中的 ***rm -rf** 命令。你可以使用 npm 安装，或者直接将 ***rimraf.js*** 放到任意位置

### API

***rimraf(f, [opts], callback)***

第一个参数视作是文件的模式匹配。如果你希望禁用模式匹配，你可以通过 ***opts.disableGlob*** 来设置，它默认为 *false* , 你必须手动去设置，比如当你的文件名中包含模式匹配中的通配符。

回调方法在被调用时，会带有一个 *error* 信息，你可以处理特定的几个错误：

- Windows: **EBUSY** 和 **ENOTEMPTY** —— ***rimraf*** 会尝试 *opts.maxBusyTries* 指定的次数直到成功完成删除操作，每次重试操作之间都会添加100毫秒的延迟，默认值为最多重试3次。
- ENOENT —— 如果文件不存在，***rimraf***会返回成功，当然我们也期望这样。
- EMFILE —— *readdir*需要打开一个文件描述符，当太多的文件描述符被占用时，就容易引发 *EMFILE* 错误。在同步模式下，不会有这个问题，但是在异步模式下，******会根据 *opts.emfileWait* 设置的的毫秒数自动等待。这个默认值为 1000ms。


### options

- unlink, chmod, stat, lstat, rmdir, readdir, unlinkSync, chmodSync, statSync, lstatSync, rmdirSync, readdirSync
如果需要使用自定义文件系统库，你可以在 *options* 对象中 重写特定的 ***fs*** 方法。***rimraf***会使用你提供的新方法替换默认的 ***fs*** 方法。
当然，同步方法仅仅与 ***rimraf.sync()*** 相关。
例如：

```javascript
var myCustomFS = require('some-custom-fs');
rimraf('some-thing', myCustomFS, callback);
```

- maxBusyTries
如果 Windows 系统上出现了 ***EBUSY***, ***ENOTEMPTY***, 或者 ***EPERM*** 错误，***rimraf*** 会在一定延时后重试，每次重试都会增加100ms后才重试。
仅适用于异步模式。

- emfileWait
如果遇到了 ***EMFILE*** 错误，***rimraf*** 会每隔一段时间重试一次，每次重试都会多等待 1ms，直到达到了最大的重试次数。默认最大重试次数为1000。如果频繁的发生 ***EMFILE*** 错误，考虑是不是使用了 ***graceful-fs***。
仅适用于异步模式。

- glob
设为 *false* 来禁用模式匹配。
设为对象来向 **glob** 模块传递自定义配置。
默认的配置如下：
```javascript
var options = { nosort: true, silent: true };
```
本模块中使用的 *glob* 的版本为6.
适用于同步异步模式。

- disableGlob
设置为任何不为假的值来禁用模式匹配。（这个与将 *glob* 设置为 *false*）。


### rimraf.sync
它也可以同步的移除。但是它不够好，推荐使用异步API，这样更好。

### mkdirp
如果你需要递归创建目录，可以查看 ***[mkdirp](https://github.com/substack/node-mkdirp)***

## gulp-rimraf

接下来进入 ***gulp-rimraf***

那么什么时候用 ***rimraf***，什么时候用 ***gulp-rimraf***。

- 删除一个文件夹，直接使用 *rimraf*， 根本不需要使用 *gulp-rimraf*。

```javascript
var rimraf = require('rimraf'); // rimraf directly
gulp.task('task', function (cb) {
rimraf('./folder', cb);
});
```
- 删除文件夹下面的文件，请使用 *gulp-rimraf*。

```javascript
var ignore = require('gulp-ignore');
var rimraf = require('gulp-rimraf');

gulp.task('task', function() {
 return gulp.src('./**/*.js', { read: false }) // much faster
   .pipe(ignore('node_modules/**'))
   .pipe(rimraf());
});
```
> 将 *read* 设置为 *false*来避免 *gulp* 读取文件的内容，这样可以变的更快。

当前工作目录外的文件夹可以使用 *force* 选项来删除。

```javascript
var rimraf = require('gulp-rimraf');

gulp.task('task', function() {
 return gulp.src('../temp/*.js', { read: false })
   .pipe(rimraf({ force: true }));
});
```



