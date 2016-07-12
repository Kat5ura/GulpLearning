# Gulp 插件 —— gulp-cache

> 一个 gulp 的缓存代理插件，他是一个基于临时文件的缓存代理任务。

## 用法

```javascript
var fs = require('fs');

var gulp = require('gulp');
var jshint = require('gulp-jshint');
var cache = require('gulp-cache');

gulp.task('lint', function() {
  gulp.src('./lib/*.js')
    .pipe(cache(jshint('.jshintrc'), {
      key: makeHashKey,
      // What on the result indicates it was successful
      success: function (jshintedFile) {
        return jshintedFile.jshint.success;
      },
      // What to store as the result of the successful action
      value: function(jshintedFile) {
        // Will be extended onto the file object on a cache hit next time task is ran
        return {
          jshint: jshintedFile.jshint
        };
      }
    }))
    .pipe(jshint.reporter('default'));
});

var jsHintVersion = '2.4.1',
  jshintOptions = fs.readFileSync('.jshintrc');
function makeHashKey(file) {
  // Key off the file contents, jshint version and options
  return [file.contents.toString('utf8'), jshintVersion, jshintOptions].join('');
}
```

## 清空缓存

如果你需要清空缓存，这里有一个华丽丽的手动方法：**cache.clearAll()**

```javascript
var cache = require('gulp-cache');

gulp.task('clear', function (done) {
  return cache.clearAll(done);
});
```

现在你可以运行 ***gulp clear*** 来清空缓存了。

## Options

### fileCache
> （可选）指定何处存放缓存对象。

 - 默认为 ***new Cache({ cacheDirName: 'gulp-cache' })***
 - 也可以使用 ***new cache.Cache({ cacheDirName: 'custom-cache' })*** 来自定义。

### name
> （可选）存放缓存对象的容器的名字。

 - 默认为 ***default**

### key
> （可选）指定缓存文件的唯一标识。

 - 可以返回一个字符串，也可以是一个能够 resolve 得到一个字符串的 *promise*， 也可以接受一个 node 的异步回调。
 - 这个方法的结果会自动转换为 MD5,不需要你去做。
 - 默认的，如果是一个 Buffer，则返回 *file.contents*；如果的一个 Stream，则返回 *undefined*。

### success
> （可选） 成功回调.

 - 必须返回一个真假值，它决定是否需要缓存任务的结果。
 - 默认为 ***true***, 这样任何任务的结果都能被缓存。

### value
> （可选）指定将什么作为任务的结果来缓存起来。

 - 可以是一个方法，它可以返回一个对象或者返回一个最终能够 resove 成一个对象的 ***promise***。另外他也可以接受一个 node 样式的异步回调。

 - 也可以被设置为一个字符串，它可以从任务结果文件中被提取出来（使用 ***_.pick***）。

 - 这个方法的结果是 **JSON.stringify** 格式化过的，它被放在一个临时文件中供后续使用。

 - 默认值为 *contents*， 它将会抓取结果文件的  ***file.contents***,然后存为字符串。




